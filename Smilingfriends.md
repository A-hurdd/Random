#include <iostream>
#include <vector>
#include <string>
#include <memory>
#include <thread>
#include <mutex>
#include <queue>
#include <random>
#include <condition_variable>

// Base class for clients
class Client {
protected:
    std::string name;
public:
    Client(const std::string& name) : name(name) {}
    virtual std::string getName() const { return name; }
    virtual ~Client() = default;
};

// Derived client class that includes specific problems
class SadClient : public Client {
private:
    std::string problem;
public:
    SadClient(const std::string& name, const std::string& problem)
        : Client(name), problem(problem) {}

    std::string getProblem() const { return problem; }
};

// Base class for agents
class SmilingAgent {
protected:
    std::string agentName;
public:
    SmilingAgent(std::string name) : agentName(std::move(name)) {}
    virtual void handleClient(const SadClient& client) = 0;
    virtual ~SmilingAgent() = default;
};

// Derived agent class that solves specific client problems
class HappinessSpecialist : public SmilingAgent {
public:
    HappinessSpecialist(const std::string& name) : SmilingAgent(name) {}

    void handleClient(const SadClient& client) override {
        std::cout << "[Agent " << agentName << "] Helping client " 
                  << client.getName() << " with problem: \"" 
                  << client.getProblem() << "\"...\n";
        std::this_thread::sleep_for(std::chrono::seconds(1));
        std::cout << "[Agent " << agentName << "] Solution complete! "
                  << client.getName() << " is now smiling!\n";
    }
};

// The dispatch center handles assigning clients to agents
class DispatchCenter {
private:
    std::vector<std::shared_ptr<SmilingAgent>> agents;
    std::queue<std::shared_ptr<SadClient>> clientQueue;
    std::mutex m;
    std::condition_variable cv;

public:
    void addAgent(const std::shared_ptr<SmilingAgent>& agent) {
        agents.push_back(agent);
    }

    void addClient(const std::shared_ptr<SadClient>& client) {
        std::unique_lock<std::mutex> lock(m);
        clientQueue.push(client);
        cv.notify_one();
    }

    void processClients() {
        while (true) {
            std::shared_ptr<SadClient> client;
            {
                std::unique_lock<std::mutex> lock(m);
                cv.wait(lock, [this]() { return !clientQueue.empty(); });
                client = clientQueue.front();
                clientQueue.pop();
            }

            // Randomly assign an agent to the client
            auto agentIndex = rand() % agents.size();
            agents[agentIndex]->handleClient(*client);

            if (clientQueue.empty()) {
                std::cout << "[Dispatch Center] All clients have been helped! Taking a break...\n";
                std::this_thread::sleep_for(std::chrono::seconds(2)); // Simulate a break period
            }
        }
    }
};

int main() {
    // Initialize agents and clients
    auto agent1 = std::make_shared<HappinessSpecialist>("Pim");
    auto agent2 = std::make_shared<HappinessSpecialist>("Charlie");

    DispatchCenter dispatchCenter;
    dispatchCenter.addAgent(agent1);
    dispatchCenter.addAgent(agent2);

    // Spawn a thread to process clients
    std::thread dispatchThread(&DispatchCenter::processClients, &dispatchCenter);

    // Add some clients with various problems to the queue
    std::vector<std::shared_ptr<SadClient>> clients = {
        std::make_shared<SadClient>("Mr. Frog", "Lost his favorite lily pad."),
        std::make_shared<SadClient>("Alan", "Struggling with existential dread."),
        std::make_shared<SadClient>("Glep", "Accidentally swallowed his phone."),
        std::make_shared<SadClient>("Enchanted Forest Elf", "Forgot how to smile."),
    };

    for (const auto& client : clients) {
        dispatchCenter.addClient(client);
        std::this_thread::sleep_for(std::chrono::seconds(1));
    }

    // Stop after processing (for demo purposes, kill the thread gracefully)
    std::this_thread::sleep_for(std::chrono::seconds(10));
    dispatchThread.detach();

    std::cout << "[Main] The Smiling Friends went home for the day.\n";
    return 0;
}