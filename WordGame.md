// Abigail Hurd
// Word Game


#include <iostream>
#include <string>

int main() {
    std::string name, city, college, profession, animal, petName;
    int age;

    // Get user input
    std::cout << "Enter your name: ";
    std::getline(std::cin, name);

    std::cout << "Enter your age: ";
    std::cin >> age;
    std::cin.ignore(); // Ignore the newline character left in the buffer

    std::cout << "Enter the name of a city: ";
    std::getline(std::cin, city);

    std::cout << "Enter the name of a college: ";
    std::getline(std::cin, college);

    std::cout << "Enter a profession: ";
    std::getline(std::cin, profession);

    std::cout << "Enter a type of animal: ";
    std::getline(std::cin, animal);

    std::cout << "Enter a pet's name: ";
    std::getline(std::cin, petName);

    // Display the story
    std::cout << "There once was a person named " << name << " who lived in " << city << ". At the age of " << age << ", " << name << " went to college at " << college << ". " << name << " graduated and went to work as a " << profession << ". Then, " << name << " adopted a(n) " << animal << " named " << petName << ". They both lived happily ever after!" << std::endl;

    return 0;
}
