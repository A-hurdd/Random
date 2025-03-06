//Abigail Hurd
//Simple Library Management System

Similar to my Bank system, a little more features

#include <iostream>
#include <vector>
#include <string>
#include <stdexcept>

class Book {
private:
    std::string title;
    std::string author;
    bool is_borrowed;

public:
    Book(const std::string& title, const std::string& author)
        : title(title), author(author), is_borrowed(false) {
    }

    void borrow() {
        if (is_borrowed) {
            throw std::runtime_error("Book is already borrowed.");
        }
        is_borrowed = true;
        std::cout << "You have borrowed \"" << title << "\" by " << author << std::endl;
    }

    void return_book() {
        if (!is_borrowed) {
            throw std::runtime_error("Book is not borrowed.");
        }
        is_borrowed = false;
        std::cout << "You have returned \"" << title << "\" by " << author << std::endl;
    }

    void check_availability() const {
        std::cout << "\"" << title << "\" by " << author << " is " 
                  << (is_borrowed ? "not available" : "available") << std::endl;
    }

    const std::string& get_title() const {
        return title;
    }

    const std::string& get_author() const {
        return author;
    }
};

class Library {
private:
    std::vector<Book> books;

public:
    void add_book(const std::string& title, const std::string& author) {
        books.emplace_back(title, author);
        std::cout << "Book \"" << title << "\" by " << author << " added to the library." << std::endl;
    }

    Book* find_book(const std::string& title) {
        for (auto& book : books) {
            if (book.get_title() == title) {
                return &book;
            }
        }
        return nullptr;
    }

    void find_books_by_author(const std::string& author) const {
        bool found = false;
        for (const auto& book : books) {
            if (book.get_author() == author) {
                std::cout << "\"" << book.get_title() << "\" by " << author << std::endl;
                found = true;
            }
        }
        if (!found) {
            std::cout << "No books found by " << author << std::endl;
        }
    }
};

int main() {
    Library library;
    int choice;
    std::string title, author;

    while (true) {
        std::cout << "\nLibrary Management System Menu:\n";
        std::cout << "1. Add Book\n";
        std::cout << "2. Borrow Book\n";
        std::cout << "3. Return Book\n";
        std::cout << "4. Check Availability\n";
        std::cout << "5. Search Books by Author\n";
        std::cout << "6. Exit\n";
        std::cout << "Enter your choice: ";
        std::cin >> choice;

        try {
            switch (choice) {
            case 1:
                std::cout << "Enter book title: ";
                std::cin.ignore();
                std::getline(std::cin, title);
                std::cout << "Enter book author: ";
                std::getline(std::cin, author);
                library.add_book(title, author);
                break;
            case 2:
                std::cout << "Enter book title: ";
                std::cin.ignore();
                std::getline(std::cin, title);
                if (Book* book = library.find_book(title)) {
                    book->borrow();
                } else {
                    throw std::runtime_error("Book not found.");
                }
                break;
            case 3:
                std::cout << "Enter book title: ";
                std::cin.ignore();
                std::getline(std::cin, title);
                if (Book* book = library.find_book(title)) {
                    book->return_book();
                } else {
                    throw std::runtime_error("Book not found.");
                }
                break;
            case 4:
                std::cout << "Enter book title: ";
                std::cin.ignore();
                std::getline(std::cin, title);
                if (Book* book = library.find_book(title)) {
                    book->check_availability();
                } else {
                    throw std::runtime_error("Book not found.");
                }
                break;
            case 5:
                std::cout << "Enter author name: ";
                std::cin.ignore();
                std::getline(std::cin, author);
                library.find_books_by_author(author);
                break;
            case 6:
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

