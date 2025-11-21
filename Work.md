#include <iostream>
#include <string>
#include <memory>
#include <vector>
#include <map>
#include <unordered_map>
#include <functional>
#include <algorithm>

// Abstract base class for items
class Item {
public:
    virtual std::string getId() const = 0;
    virtual std::string getName() const = 0;
    virtual ~Item() = default;
};

// Templated derived class for specific items
template <typename T>
class SpecificItem : public Item {
private:
    std::string id;
    std::string name;
    T extraInfo; // Extra information about the item
public:
    SpecificItem(const std::string& id, const std::string& name, const T& extraInfo)
        : id(id), name(name), extraInfo(extraInfo) {}

    std::string getId() const override { 
        return id; 
    }

    std::string getName() const override { 
        return name; 
    }

    T getExtraInfo() const { 
        return extraInfo; 
    }
};

// Abstract base class for compartments
class Compartment {
public:
    virtual void addItem(std::shared_ptr<Item> item) = 0;
    virtual void displayItems() const = 0;
    virtual ~Compartment() = default;
};

// Derived class for specific compartments
class SpecificCompartment : public Compartment {
private:
    std::string id;
    std::string category;
    std::vector<std::shared_ptr<Item>> items;

public:
    SpecificCompartment(const std::string& id, const std::string& category)
        : id(id), category(category) {}

    void addItem(std::shared_ptr<Item> item) override {
        items.push_back(item);
    }

    void displayItems() const override {
        std::cout << "Compartment " << id << " (" << category << ") contains:\n";
        for (const auto& item : items) {
            std::cout << "  - " << item->getName() << " [ID: " << item->getId() << "]\n";
        }
    }

    std::string getId() const {
        return id;
    }
};

// Inventory Manager class
class InventoryManager {
private:
    std::unordered_map<std::string, std::shared_ptr<Compartment>> compartments;

public:
    void addCompartment(const std::shared_ptr<Compartment>& compartment) {
        compartments[compartment->getId()] = compartment;
    }

    void addItemToCompartment(const std::string& compartmentId, std::shared_ptr<Item> item) {
        if (compartments.find(compartmentId) != compartments.end()) {
            compartments[compartmentId]->addItem(item);
        } else {
            std::cerr << "Error: Compartment ID " << compartmentId << " not found.\n";
        }
    }

    void displayInventory() const {
        for (const auto& pair : compartments) {
            pair.second->displayItems();
            std::cout << std::endl;
        }
    }
};

int main() {
    // Create an overly complex inventory system
    InventoryManager manager;

    // Create some compartments
    auto statPackCompartment = std::make_shared<SpecificCompartment>("A1", "Stat Packs");
    auto lifePackCompartment = std::make_shared<SpecificCompartment>("B2", "Life Packs");
    auto oxygenTankCompartment = std::make_shared<SpecificCompartment>("C3", "Oxygen Tanks");

    // Add compartments to manager
    manager.addCompartment(statPackCompartment);
    manager.addCompartment(lifePackCompartment);
    manager.addCompartment(oxygenTankCompartment);

    // Create some items
    auto statPack = std::make_shared<SpecificItem<int>>("SP01", "Stat Pack Alpha", 5);
    auto lifePack = std::make_shared<SpecificItem<double>>("LP01", "Life Pack Model 15", 2.7);
    auto oxygenTank = std::make_shared<SpecificItem<std::string>>("OX01", "Oxygen Tank A", "Refill Date: 2025-12-01");

    // Add items to compartments
    manager.addItemToCompartment("A1", statPack);
    manager.addItemToCompartment("B2", lifePack);
    manager.addItemToCompartment("C3", oxygenTank);

    // Display the inventory
    manager.displayInventory();

    return 0;
}