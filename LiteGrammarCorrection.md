// Abigail Hurd 
//Grammar Correction

#include <iostream>
#include <string>
#include <cctype>
#include <sstream>
#include <vector>
#include <unordered_map>

// Function to correct capitalization and punctuation
std::string correct_sentence(const std::string& sentence) {
    std::string corrected = sentence;
    bool capitalize_next = true;

    for (size_t i = 0; i < corrected.length(); ++i) {
        if (capitalize_next && std::islower(corrected[i])) {
            corrected[i] = std::toupper(corrected[i]);
            capitalize_next = false;
        }
        else if (corrected[i] == '.' || corrected[i] == '!' || corrected[i] == '?') {
            capitalize_next = true;
        }
        else if (std::isspace(corrected[i])) {
            capitalize_next = false;
        }
    }

    // Ensure the sentence ends with a period
    if (!corrected.empty() && corrected.back() != '.' && corrected.back() != '!' && corrected.back() != '?') {
        corrected += '.';
    }

    return corrected;
}

// Function to ensure proper spacing after punctuation
std::string ensure_spacing(const std::string& sentence) {
    std::string corrected;
    bool space_needed = false;

    for (char ch : sentence) {
        if (space_needed && !std::isspace(ch)) {
            corrected += ' ';
            space_needed = false;
        }
        corrected += ch;
        if (ch == '.' || ch == ',' || ch == '!' || ch == '?') {
            space_needed = true;
        }
        else if (!std::isspace(ch)) {
            space_needed = false;
        }
    }

    return corrected;
}

// Function to correct common contractions and abbreviations
std::string correct_contractions(const std::string& sentence) {
    std::unordered_map<std::string, std::string> contractions = {
        {"dont", "don't"},
        {"wont", "won't"},
        {"cant", "can't"},
        {"im", "I'm"},
        {"ive", "I've"},
        {"youre", "you're"},
        {"theyre", "they're"},
        {"isnt", "isn't"},
        {"arent", "aren't"},
        {"wasnt", "wasn't"},
        {"werent", "weren't"},
        {"hasnt", "hasn't"},
        {"havent", "haven't"},
        {"hadnt", "hadn't"},
        {"doesnt", "doesn't"},
        {"dont", "don't"},
        {"didnt", "didn't"},
        {"wont", "won't"},
        {"wouldnt", "wouldn't"},
        {"cant", "can't"},
        {"couldnt", "couldn't"},
        {"shouldnt", "shouldn't"},
        {"mightnt", "mightn't"},
        {"mustnt", "mustn't"}
    };

    std::istringstream iss(sentence);
    std::string word;
    std::string corrected;

    while (iss >> word) {
        auto it = contractions.find(word);
        if (it != contractions.end()) {
            corrected += it->second;
        }
        else {
            corrected += word;
        }
        corrected += ' ';
    }

    if (!corrected.empty()) {
        corrected.pop_back(); // Remove the trailing space
    }

    return corrected;
}

int main() {
    std::string sentence;

    std::cout << "Enter a sentence: ";
    std::getline(std::cin, sentence);

    std::string corrected_sentence = correct_sentence(sentence);
    corrected_sentence = ensure_spacing(corrected_sentence);
    corrected_sentence = correct_contractions(corrected_sentence);

    std::cout << "Corrected sentence: " << corrected_sentence << std::endl;

    return 0;
}
