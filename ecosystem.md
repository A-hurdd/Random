#include <iostream>
#include <vector>
#include <memory>
#include <string>
#include <random>
#include <thread>
#include <chrono>
#include <map>

// Random number generator
std::random_device rd;
std::mt19937 gen(rd());

// Abstract base class for all living entities
class LivingEntity {
public:
    virtual std::string getName() const = 0;
    virtual void act() const = 0; // Polymorphic behavior
    virtual ~LivingEntity() = default;
};

// Observer interface for event notifications
class Observer {
public:
    virtual void onEvent(const std::string& event) = 0;
    virtual ~Observer() = default;
};

// Subject class for managing observers
class Subject {
private:
    std::vector<Observer*> observers;
public:
    void attach(Observer* obs) {
        observers.push_back(obs);
    }

    void notify(const std::string& event) {
        for (Observer* obs : observers) {
            obs->onEvent(event);
        }
    }
};

// Template class for specific animal types
template <typename T>
class Animal : public LivingEntity {
private:
    std::string name;
    T uniqueTrait;

public:
    Animal(const std::string& name, const T& trait) : name(name), uniqueTrait(trait) {}

    std::string getName() const override {
        return name;
    }

    void act() const override {
        std::cout << name << " (" << uniqueTrait << ") is acting!" << std::endl;
    }
};

// Concrete observer for logging events
class Logger : public Observer {
public:
    void onEvent(const std::string& event) override {
        std::cout << "[LOG] " << event << std::endl;
    }
};

// Concrete observer for tracking health
class HealthTracker : public Observer {
private:
    std::map<std::string, int> healthMap;
public:
    void onEvent(const std::string& event) override {
        if (event.find("disease") != std::string::npos) {
            std::string name = event.substr(0, event.find(" "));
            healthMap[name] -= 20;
            std::cout << "[HEALTH] " << name << "'s health reduced! Current health: " 
                      << healthMap[name] << std::endl;
        }
    }

    void addEntity(const std::string& name) {
        healthMap[name] = 100; // Everyone starts with full health
    }
};

// Ecosystem manager class
class Ecosystem : public Subject {
private:
    std::vector<std::unique_ptr<LivingEntity>> entities;

public:
    void addEntity(std::unique_ptr<LivingEntity> entity) {
        std::cout << "Adding entity: " << entity->getName() << std::endl;
        notify(entity->getName() + " has joined the ecosystem.");
        entities.push_back(std::move(entity));
    }

    void simulate() {
        std::uniform_int_distribution<> eventDist(0, 5);
        for (size_t i = 0; i < 10; ++i) {
            for (const auto& entity : entities) {
                entity->act();
            }

            int randomEvent = eventDist(gen);
            if (randomEvent == 1) {
                std::string affected = entities[gen() % entities.size()]->getName();
                notify(affected + " caught a disease!");
            } else if (randomEvent == 2) {
                std::string affected = entities[gen() % entities.size()]->getName();
                notify(affected + " found food and became stronger!");
            }

            std::this_thread::sleep_for(std::chrono::milliseconds(500));
        }
    }
};

int main() {
    // Create some observers
    auto logger = std::make_unique<Logger>();
    auto healthTracker = std::make_unique<HealthTracker>();

    // Create the ecosystem
    Ecosystem ecosystem;
    ecosystem.attach(logger.get());
    ecosystem.attach(healthTracker.get());

    // Add entities
    auto wolf = std::make_unique<Animal<std::string>>("Wolf Alpha", "Pack Leader");
    auto rabbit = std::make_unique<Animal<int>>("Rabbit Charlie", 10);  // 10 offspring potential
    auto deer = std::make_unique<Animal<bool>>("Deer Delta", true);    // Migratory creature

    healthTracker->addEntity(wolf->getName());
    healthTracker->addEntity(rabbit->getName());
    healthTracker->addEntity(deer->getName());

    ecosystem.addEntity(std::move(wolf));
    ecosystem.addEntity(std::move(rabbit));
    ecosystem.addEntity(std::move(deer));

    // Simulate ecosystem dynamics
    ecosystem.simulate();

    return 0;
}