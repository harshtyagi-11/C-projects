# C-projects task 1

Student Management system using c++ programming language
here is its code:

    #include <iostream>
    #include <fstream>
    #include <cstring>
    #include <limits>
    #include <string>
    #include <cstdio>

    using namespace std;

    class Student {
    public:
    int rollNo;
    char name[50];
    char grade[10];
    int marks[3];
    float gpa;
    int attendance;

    void input() {
        cout << "Enter Roll No: ";
        cin >> rollNo;
        cin.ignore(numeric_limits<streamsize>::max(), '\n');

        cout << "Enter Name: ";
        cin.getline(name, 50);

        cout << "Enter Grade: ";
        cin.getline(grade, 10);

        cout << "Enter Marks (out of 100):\n";
        cout << "Math: ";
        cin >> marks[0];
        cout << "Science: ";
        cin >> marks[1];
        cout << "English: ";
        cin >> marks[2];

        calculateGPA();

        cout << "Enter Attendance (out of 100 days): ";
        cin >> attendance;

        cin.ignore(numeric_limits<streamsize>::max(), '\n');
    }

    void calculateGPA() {
        float avg = (marks[0] + marks[1] + marks[2]) / 3.0;
        gpa = avg / 20.0;
    }

    void display() const {
        cout << "Roll No: " << rollNo << "\n";
        cout << "Name: " << name << "\n";
        cout << "Grade: " << grade << "\n";
        cout << "Marks:\n";
        cout << "  Math: " << marks[0] << "\n";
        cout << "  Science: " << marks[1] << "\n";
        cout << "  English: " << marks[2] << "\n";
        cout << "GPA: " << gpa << "\n";
        cout << "Attendance: " << attendance << "/100\n";
    }
    
    bool login() {
    string user, pass;
    cout << "--- Admin Login ---\n";
    cout << "Username: ";
    cin >> user;
    cout << "Password: ";
    cin >> pass;

    if (user == "Admin" && pass == "Pass@345") {
        cout << "Login successful.\n";
        return true;
    } else {
        cout << "Invalid credentials. Exiting.\n";
        return false;
    }
    }

    void addStudent() {
    Student s;
    s.input();
    ofstream outFile("students.dat", ios::binary | ios::app);
    if (!outFile.is_open()) {
        cout << "Error: Could not open file for writing.\n";
        return;
    }
    outFile.write(reinterpret_cast<char*>(&s), sizeof(Student));
    outFile.close();
    cout << "Student added successfully!\n";
    }

    void displayAll() {
    Student s;
    ifstream inFile("students.dat", ios::binary);
    if (!inFile.is_open()) {
        cout << "No student records found or file could not be opened.\n";
        return;
    }
    cout << "\nAll Student Records:\n";
    while (inFile.read(reinterpret_cast<char*>(&s), sizeof(Student))) {
        s.display();
        cout << "---------------------\n";
    }
    inFile.close();
    }

    void searchStudent(int rollNo) {
    Student s;
    ifstream inFile("students.dat", ios::binary);
    if (!inFile.is_open()) {
        cout << "No student records found or file could not be opened.\n";
        return;
    }
    bool found = false;

    while (inFile.read(reinterpret_cast<char*>(&s), sizeof(Student))) {
        if (s.rollNo == rollNo) {
            cout << "\nStudent Found:\n";
            s.display();
            found = true;
            break;
        }
    }
    inFile.close();

    if (!found)
        cout << "Student not found.\n";
    }

    void deleteStudent(int rollNo) {
    Student s;
    ifstream inFile("students.dat", ios::binary);
    if (!inFile.is_open()) {
        cout << "No student records found or file could not be opened.\n";
        return;
    }
    ofstream outFile("temp.dat", ios::binary);
    if (!outFile.is_open()) {
        cout << "Error: Could not create temporary file for deletion.\n";
        inFile.close();
        return;
    }

    bool found = false;

    while (inFile.read(reinterpret_cast<char*>(&s), sizeof(Student))) {
        if (s.rollNo != rollNo) {
            outFile.write(reinterpret_cast<char*>(&s), sizeof(Student));
        } else {
            found = true;
        }
    }

    inFile.close();
    outFile.close();
    remove("students.dat");
    rename("temp.dat", "students.dat");

    if (found)
        cout << "Student deleted successfully.\n";
    else
        cout << "Student not found.\n";
    }

    void updateStudent(int rollNo) {
    Student s;
    fstream file("students.dat", ios::binary | ios::in | ios::out);
    if (!file.is_open()) {
        cout << "No student records found or file could not be opened.\n";
        return;
    }
    bool found = false;

    while (file.read(reinterpret_cast<char*>(&s), sizeof(Student))) {
        if (s.rollNo == rollNo) {
            cout << "Enter new details:\n";
            s.input();
            file.seekp(-static_cast<int>(sizeof(Student)), ios::cur);
            file.write(reinterpret_cast<char*>(&s), sizeof(Student));
            found = true;
            cout << "Student record updated.\n";
            break;
        }
    }
    file.close();

    if (!found)
        cout << "Student not found.\n";
    }

    int main() {
    if (!login())
        return 0;

    int choice, rollNo;

    do {
        cout << "\n--- Student Management System ---\n";
        cout << "1. Add Student\n";
        cout << "2. Display All Students\n";
        cout << "3. Search Student\n";
        cout << "4. Update Student\n";
        cout << "5. Delete Student\n";
        cout << "0. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;
        cin.ignore(numeric_limits<streamsize>::max(), '\n');

        switch (choice) {
        case 1:
            addStudent();
            break;
        case 2:
            displayAll();
            break;
        case 3:
            cout << "Enter Roll No to search: ";
            cin >> rollNo;
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            searchStudent(rollNo);
            break;
        case 4:
            cout << "Enter Roll No to update: ";
            cin >> rollNo;
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            updateStudent(rollNo);
            break;
        case 5:
            cout << "Enter Roll No to delete: ";
            cin >> rollNo;
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            deleteStudent(rollNo);
            break;
        case 0:
            cout << "Exiting...\n";
            break;
        default:
            cout << "Invalid choice.\n";
        }

    } while (choice != 0);

    return 0;
    }


This system contain login security system in which admin has to enter its id and password
then, a log opens contains:
--- Student Management System ---
1. Add Student
2. Display All Students
3. Search Student
4. Update Student
5. Delete Student
0. Exit
Enter your choice:

enter you choice to go further, like 1st choice is to add student. we choose 1, now new log opens for details like roll no., name of student, grade,
Marks in different subjects(like math, science, english), attendance:
--- Student Management System ---
1. Add Student
2. Display All Students
3. Search Student
4. Update Student
5. Delete Student
0. Exit
Enter your choice: 1
Enter Roll No: 6
Enter Name: Harsh
Enter Grade: 12
Enter Marks (out of 100):
Math: 96
Science: 98
English: 95
Enter Attendance (out of 100 days): 81
Student added successfully!

now, choose option 2 for display of student:
--- Student Management System ---
1. Add Student
2. Display All Students
3. Search Student
4. Update Student
5. Delete Student
0. Exit
Enter your choice: 2

All Student Records:
Roll No: 6
Name: Harsh
Grade: 12
Marks:
  Math: 96
  Science: 98
  English: 95
GPA: 4.81667
Attendance: 81/100
---------------------

now, to exit the application choose 0
--- Student Management System ---
1. Add Student
2. Display All Students
3. Search Student
4. Update Student
5. Delete Student
0. Exit
Enter your choice: 0
Exiting...

--------------------------------
Process exited after 339.2 seconds with return value 0
Press any key to continue . . .

Here, is the whole programming code.

Thanks 
Harsh
