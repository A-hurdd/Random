//Abigail Hurd
//Simple Banking System

#include <iostream>
#include <vector>
#include <string>
#include <stdexcept>

class Account {
private:
    std::string owner;
    unsigned long long balance;

public:
    Account(const std::string& owner, unsigned long long initial_balance)
        : owner(owner), balance(initial_balance) {
    }

    void deposit(unsigned long long amount) {
        if (amount <= 0) {
            throw std::invalid_argument("Deposit amount must be positive.");
        }
        balance += amount;
        std::cout << "Deposited " << amount << ". New balance: " << balance << std::endl;
    }

    void withdraw(unsigned long long amount) {
        if (amount <= 0) {
            throw std::invalid_argument("Withdrawal amount must be positive.");
        }
        if (amount > balance) {
            throw std::runtime_error("Insufficient funds. Withdrawal failed.");
        }
        balance -= amount;
        std::cout << "Withdrew " << amount << ". New balance: " << balance << std::endl;
    }

    void check_balance() const {
        std::cout << "Current balance: " << balance << std::endl;
    }

    const std::string& get_owner() const {
        return owner;
    }
};

class Bank {
private:
    std::vector<Account> accounts;

public:
    void create_account(const std::string& owner, unsigned long long initial_balance) {
        if (initial_balance < 0) {
            throw std::invalid_argument("Initial balance must be non-negative.");
        }
        accounts.emplace_back(owner, initial_balance);
        std::cout << "Account created for " << owner << " with initial balance " << initial_balance << std::endl;
    }

    Account* find_account(const std::string& owner) {
        for (auto& account : accounts) {
            if (account.get_owner() == owner) {
                return &account;
            }
        }
        return nullptr;
    }
};

int main() {
    Bank bank;
    int choice;
    std::string owner;
    unsigned long long amount;

    while (true) {
        std::cout << "\nBanking System Menu:\n";
        std::cout << "1. Create Account\n";
        std::cout << "2. Deposit\n";
        std::cout << "3. Withdraw\n";
        std::cout << "4. Check Balance\n";
        std::cout << "5. Exit\n";
        std::cout << "Enter your choice: ";
        std::cin >> choice;

        try {
            switch (choice) {
            case 1:
                std::cout << "Enter account owner's name: ";
                std::cin >> owner;
                std::cout << "Enter initial balance: ";
                std::cin >> amount;
                bank.create_account(owner, amount);
                break;
            case 2:
                std::cout << "Enter account owner's name: ";
                std::cin >> owner;
                std::cout << "Enter amount to deposit: ";
                std::cin >> amount;
                if (Account* account = bank.find_account(owner)) {
                    account->deposit(amount);
                } else {
                    throw std::runtime_error("Account not found.");
                }
                break;
            case 3:
                std::cout << "Enter account owner's name: ";
                std::cin >> owner;
                std::cout << "Enter amount to withdraw: ";
                std::cin >> amount;
                if (Account* account = bank.find_account(owner)) {
                    account->withdraw(amount);
                } else {
                    throw std::runtime_error("Account not found.");
                }
                break;
            case 4:
                std::cout << "Enter account owner's name: ";
                std::cin >> owner;
                if (Account* account = bank.find_account(owner)) {
                    account->check_balance();
                } else {
                    throw std::runtime_error("Account not found.");
                }
                break;
            case 5:
                std::cout << "Exiting the system. Goodbye!" << std::endl;
                return 0;
            default:
                std::cout << "Invalid choice. Please try again." << std::endl;
            }
        } catch (const std::exception& e) {
            std::cout << "Error: " << e.what() << std::endl;
        }
    }

    return 0;
}
