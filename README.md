import json
import os
from datetime import datetime

class Task:
    def _init_(self, description, priority, due_date=None, completed=False):
        self.description = description
        self.priority = priority
        self.due_date = due_date
        self.completed = completed

    def _str_(self):
        status = "Completed" if self.completed else "Pending"
        return f"{self.description} - Priority: {self.priority}, Due: {self.due_date}, Status: {status}"

class ToDoList:
    def _init_(self):
        self.tasks = []

    def add_task(self, task):
        self.tasks.append(task)

    def remove_task(self, task_index):
        del self.tasks[task_index]

    def mark_task_completed(self, task_index):
        self.tasks[task_index].completed = True

    def save_tasks(self, filename):
        with open(filename, "w") as f:
            json.dump([task._dict_ for task in self.tasks], f)

    def load_tasks(self, filename):
        if os.path.exists(filename):
            with open(filename, "r") as f:
                data = json.load(f)
                self.tasks = [Task(**task) for task in data]

def main():
    filename = "tasks.json"
    todo_list = ToDoList()
    todo_list.load_tasks(filename)

    while True:
        print("\n===== ToDo List =====")
        for i, task in enumerate(todo_list.tasks):
            print(f"{i + 1}. {task}")

        print("\nMenu:")
        print("1. Add Task")
        print("2. Remove Task")
        print("3. Mark Task as Completed")
        print("4. Save and Quit")

        choice = input("Enter your choice: ")

        if choice == "1":
            description = input("Enter task description: ")
            priority = input("Enter task priority (high/medium/low): ")
            due_date = input("Enter due date (YYYY-MM-DD): ")
            if due_date:
                due_date = datetime.strptime(due_date, "%Y-%m-%d").date()
            task = Task(description, priority, due_date)
            todo_list.add_task(task)

        elif choice == "2":
            task_index = int(input("Enter task number to remove: ")) - 1
            todo_list.remove_task(task_index)

        elif choice == "3":
            task_index = int(input("Enter task number to mark as completed: ")) - 1
            todo_list.mark_task_completed(task_index)

        elif choice == "4":
            todo_list.save_tasks(filename)
            print("Tasks saved. Goodbye!")
            break

        else:
            print("Invalid choice. Please try again.")

if _name_ == "_main_":
    main()
