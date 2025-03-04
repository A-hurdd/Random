//Abigil Hurd
//Hours and Wage

#include <iostream>
#include <string>
#include <array>
#include <iomanip>

int main() {
    const std::array<std::string, 7> daysOfWeek = { "Sunday of last week", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday" };
    std::array<int, 7> hoursWorked = { 0 };
    int totalHours = 0;
    double hourlyWage = 0.0;

    for (int i = 0; i < daysOfWeek.size(); ++i) {
        std::cout << "Enter the number of hours worked on " << daysOfWeek[i] << ": ";
        std::cin >> hoursWorked[i];
        totalHours += hoursWorked[i];
    }

    std::cout << "Enter your hourly wage: $";
    std::cin >> hourlyWage;

    double totalEarnings = totalHours * hourlyWage;

    std::cout << "Total hours worked in the week: " << totalHours << std::endl;
    std::cout << "Total earnings for the week: $" << std::fixed << std::setprecision(2) << totalEarnings << std::endl;

    return 0;
}

