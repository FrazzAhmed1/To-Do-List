# To-Do-List
#include <iostream>
#include <string>
#include <ctime>
#include<fstream>
using namespace std;


class Todo { // class implementation
private:
    char option;
    string* TodoHold;
    int* TodoID; 
    int capacity;
    int count;
    int nextID;
public:
    void ProgramGreeting();
    void TodoManager();
    void GetTodo();
    void AddTodo();
    void RemoveTodo();
    void PrintTodo();
    void ComponentTest();
    void SystemDate();
    int GetCount() const;
    string GetTodoHold(int index) const;
    Todo();
    ~Todo();
    Todo(const Todo& diff);
    Todo& operator=(const Todo& diff);
    friend ostream& operator<<(ostream& os, const Todo& todo); // Overload
    friend istream& operator>>(istream& is, Todo& todo); // Overload
    void SaveToFile(const string& filename);
    void LoadInFromFile(const string& filename);
};

Todo::Todo() {
    capacity = 1;
    count = 0;
    nextID = 1;  
    TodoHold = new string[capacity]; 
    TodoID = new int[capacity];  
}

Todo::Todo(const Todo& diff) { // Overload Copy Constructor
    capacity = diff.capacity;
    count = diff.count;
    nextID = diff.nextID;
    TodoHold = new string[capacity];
    TodoID = new int[capacity];
    for (int i = 0; i < count; i++) {
        TodoHold[i] = diff.TodoHold[i];
        TodoID[i] = diff.TodoID[i];
    }
}

Todo& Todo::operator=(const Todo& diff) { // Overload Assignment Operator
    if (this != &diff) {
        capacity = diff.capacity;
        count = diff.count;
        nextID = diff.nextID;
        delete[] TodoHold;
        delete[] TodoID;
        TodoHold = new string[capacity];
        TodoID = new int[capacity];
        for (int i = 0; i < count; i++) {
            TodoHold[i] = diff.TodoHold[i];
            TodoID[i] = diff.TodoID[i];
        }
     }
    return *this;
}

Todo::~Todo() {
    delete[] TodoHold;  // deconstructors to clean-up 
    delete[] TodoID;
}

int Todo::GetCount() const {
    return count;
}

string Todo::GetTodoHold(int index) const {
    if (index >= 0 && index < count) {
        return TodoHold[index];
    }
    return "";
}

void Todo::ComponentTest() { // Test TODO's
    for (int i = 0; i < 5; i++) {
        TodoID[i] = nextID;
        TodoHold[i] = "Test Todo " + to_string(nextID);
        nextID++;
        count++;
   }
    cout << "Component testing TODOs" << endl;
}

void Todo::ProgramGreeting() { // Greeting Function
  cout << "todo.cpp" << endl;
  cout << "Fraz Ahmed" << endl;
  cout << "Welcome to the TODO Manager!" << endl;
  SystemDate();
}

void Todo::GetTodo() { 
    do {
        cout << "<+> to Enter a to-do item:" << endl; // Display Menu
        cout << "<-> to Remove a to-do item:" << endl;
        cout << "<?> to Display tasks:" << endl;
        cout << "<*> to Quit:" << endl;
        cin >> option;
        switch (option) {
        case '+':  // - + Symbol
            AddTodo();
            break;
        case '-':
            RemoveTodo(); // - - Symbol
            break;
        case '?':
            PrintTodo(); // - ? Symbol
            break;
        case '*':
            cout << "Ending the program now." << endl;
            break;
        default:
            cout << "Invalid option, Please try again." << endl;
            break;
        }
  } while (option != '*');
}

void Todo::AddTodo() {
    if (count == capacity) {
        string* pTmp = new string[capacity * 2]; // - TODO dynamic array
        int* idTmp = new int[capacity * 2];
        for (int i = 0; i < count; i++) {
            pTmp[i] = TodoHold[i];
            idTmp[i] = TodoID[i];
        }
        delete[] TodoHold;
        delete[] TodoID;
        TodoHold = pTmp;
        TodoID = idTmp;
       capacity *= 2;
    }

    cin.ignore();
    cout << "Enter a to-do item: " << endl;
    getline(cin, TodoHold[count]);
    TodoID[count] = nextID;  
    nextID++; 
    count++;
}

void Todo::RemoveTodo() {
    if (count > 0) {
        cout << "Which item do you want to remove? (Enter the ID #): " << endl;
        int id;
        cin >> id;

        int index = -1;
        for (int i = 0; i < count; i++) {
            if (TodoID[i] == id) {
                index = i;
                break; 
            }
     }

        if (index != -1) {

            for (int i = index; i < count - 1; i++) {
                TodoHold[i] = TodoHold[i + 1];
                TodoID[i] = TodoID[i + 1];
          }
            count--; 
            cout << "TODO item of ID " << id << " was removed." << endl;
       } else {
            cout << "TODO item of  ID " << id << " doesn't exist" << endl;
         }
       } else {
        cout << "No items to remove yet" << endl;
  }
}

void Todo::SystemDate() { // -System Date Method
  time_t currentTime = time(nullptr);
  tm* localTime = localtime(&currentTime);
  int day = localTime->tm_mday;
  int month = localTime->tm_mon + 1;
  int year = localTime->tm_year + 1900;
  cout << "Date: " << month << "/" << day << "/" << year << endl;
}

void Todo::PrintTodo() {
    for (int i = 0; i < count; i++) {

        cout << "TODO ID: " << TodoID[i] << endl;  
        SystemDate();
        cout << "TODO Item: " << TodoHold[i] << endl;
        cout << "--------------------" << endl;
    }
}

ostream& operator<<(ostream& os, const Todo& todo) {
    for (int i = 0; i < todo.count; i++) {
        os << "TODO ID: " << todo.TodoID[i] << endl;
        os << "TODO Item: " << todo.TodoHold[i] << endl;
        os << "--------------------" << endl;
    }
    return os;
}

istream& operator>>(istream& is, Todo& todo) {
    if (todo.count == todo.capacity) {
        string* pTmp = new string[todo.capacity * 2];
        int* idTmp = new int[todo.capacity * 2];
        for (int i = 0; i < todo.count; i++) {
          pTmp[i] = todo.TodoHold[i];
            idTmp[i] = todo.TodoID[i];
        }
        delete[] todo.TodoHold;
        delete[] todo.TodoID;
        todo.TodoHold = pTmp;
        todo.TodoID = idTmp;
        todo.capacity *= 2;
    }

    cout << "Enter a to-do item: " << endl;
    cin.ignore();
    getline(cin, todo.TodoHold[todo.count]);
    todo.TodoID[todo.count] = todo.nextID;  
    todo.nextID++; 
    todo.count++;
    return is;
}

void Todo::SaveToFile(const string& filename) { // - Persistence
    ofstream outfile(filename);
    if (outfile.is_open()) {
        outfile << count << endl;
      for (int i = 0; i < count; i++) {
            outfile << TodoID[i] << ", " << TodoHold[i] << endl;
        }
        outfile.close();
        cout << "TODO items saved to file: " << filename << endl;
    }
}

void Todo::LoadInFromFile(const string& filename) {
    ifstream infile(filename);
    if (infile.is_open()) {
        infile >> count;
        for (int i = 0; i < count; i++) {
            infile >> TodoID[i];
            getline(infile >> ws, TodoHold[i]);
        }
        infile.close();
        cout << "TODOs loaded from file: " << filename << endl;
    } else {
        cout << "No TODO file at the moment." << endl;
    }
}


int main() { // main utilized to call necessary functions and create object
    
    const string filename = "todoitems.txt";
    Todo MyList; // object of type Todo
    MyList.ProgramGreeting();
    MyList.LoadInFromFile(filename);
    MyList.GetTodo(); 
    MyList.SaveToFile(filename); 
    MyList.ComponentTest();
    return 0;
}
