// Abigail Hurd
// Program to calculate factorial of a number using an iterative approach

#include <iostream>

// Function to calculate factorial iteratively
unsigned long long factorial(int n) {
    unsigned long long result = 1;
    for (int i = 1; i <= n; ++i) {
        result *= i;
    }
    return result;
}

int main() {
    int number;
    std::cout << "Enter a positive integer: ";
    std::cin >> number;

    if (number < 0) {
        std::cout << "Factorial is not defined for negative numbers." << std::endl;
    }
    else {
        std::cout << "Factorial of " << number << " = " << factorial(number) << std::endl;
    }

    return 0;
}
