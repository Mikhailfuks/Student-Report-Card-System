#include <iostream>
#include <string>
#include <vector>
#include <iomanip>
#include <fstream>
#include <sstream>

using namespace std;

// Student Class
class Student {
public:
    int rollNumber;
    string name;
    vector<int> marks; // List of marks for subjects

    Student(int rollNumber, string name, const vector<int>& marks) {
        this->rollNumber = rollNumber;
        this->name = name;
        this->marks = marks;
    }

    // Calculate total marks
    int calculateTotalMarks() {
        int total = 0;
        for (int mark : marks) {
            total += mark;
        }
        return total;
    }

    // Calculate average marks
    double calculateAverageMarks() {
        return static_cast<double>(calculateTotalMarks()) / marks.size();
    }

    // Display student details
    void displayDetails() {
        cout << "Roll Number: " << rollNumber << endl;
        cout << "Name: " << name << endl;
        cout << "Marks: ";
        for (int mark : marks) {
            cout << mark << " ";
        }
        cout << endl;
        cout << "Total Marks: " << calculateTotalMarks() << endl;
        cout << "Average Marks: " << fixed << setprecision(2) << calculateAverageMarks() << endl;
        cout << "-------------------------" << endl;
    }
};

// Report Card System Class
class ReportCardSystem {
private:
    vector<Student> students;
    int nextRollNumber = 1;

public:
    // Add a new student
    void addStudent(string name, const vector<int>& marks) {
        students.push_back(Student(nextRollNumber++, name, marks));
        cout << "Student added successfully!" << endl;
    }

    // Get a student by roll number
    Student* getStudent(int rollNumber) {
        for (Student& student : students) {
            if (student.rollNumber == rollNumber) {
                return &student;
            }
        }
        return nullptr;
    }

    // Load students from a file
    void loadStudents(const string& filename) {
        ifstream file(filename);
        if (file.is_open()) {
            int rollNumber;
            string name;
            string line;
            while (getline(file, name)) {
                getline(file, line); // Read marks as a comma-separated string
                stringstream ss(line);
                vector<int> marks;
                int mark;
                while (ss >> mark) {
                    marks.push_back(mark);
                    if (ss.peek() == ',') {

                        ss.ignore(); // Ignore comma
                    }
                }
                students.push_back(Student(nextRollNumber++, name, marks));
            }
            file.close();
        } else {
            cout << "Error opening student data file!" << endl;
        }
    }

    // Save students to a file
    void saveStudents(const string& filename) {
        ofstream file(filename);
        if (file.is_open()) {
            for (Student& student : students) {
                file << student.name << endl;
                for (int mark : student.marks) {
                    file << mark << ",";
                }
                file << endl; // Add a newline after each student
            }
            file.close();
        } else {
            cout << "Error saving student data file!" << endl;
        }
    }

    // Main report card system menu
    void startReportCardSystem() {
        cout << "Welcome to the Student Report Card System!" << endl;

        while (true) {
            cout << "\nMenu:" << endl;
            cout << "1. Add Student" << endl;
            cout << "2. Display Student Report Card" << endl;
            cout << "3. Exit" << endl;
            cout << "Enter your choice: ";
            int choice;
            cin >> choice;

            switch (choice) {
                case 1: {
                    string name;
                    vector<int> marks;
                    cout << "Enter student name: ";
                    cin.ignore(); // Clear the input buffer
                    getline(cin, name);

                    cout << "Enter student marks (comma-separated): ";
                    string marksStr;
                    getline(cin, marksStr);
                    stringstream ss(marksStr);
                    int mark;
                    while (ss >> mark) {
                        marks.push_back(mark);
                        if (ss.peek() == ',') {
                            ss.ignore(); // Ignore comma
                        }
                    }

                    addStudent(name, marks);
                    break;
                }
                case 2: {
                    int rollNumber;
                    cout << "Enter student roll number: ";
                    cin >> rollNumber;
                    Student* student = getStudent(rollNumber);
                    if (student != nullptr) {
                        student->displayDetails();
                    } else {
                        cout << "Student not found." << endl;
                    }
                    break;
                }
                case 3:
                    cout << "Exiting..." << endl;
                    saveStudents("students.txt");
                    return;
                default:
                    cout << "Invalid choice." << endl;
            }
        }
    }
};

int main() {
    ReportCardSystem system;
    system.loadStudents("students.txt"); // Load students from file (if it exists)
    system.startReportCardSystem();
    return 0;
}
