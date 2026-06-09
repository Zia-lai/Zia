# Employee Management System
import sqlite3

DB_NAME = "employees.db"


def create_table():
    conn = sqlite3.connect(DB_NAME)
    cursor = conn.cursor()

    cursor.execute("""
    CREATE TABLE IF NOT EXISTS employees (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT NOT NULL,
        department TEXT NOT NULL,
        salary REAL NOT NULL
    )
    """)

    conn.commit()
    conn.close()


def add_employee():
    name = input("Employee Name: ")
    department = input("Department: ")

    try:
        salary = float(input("Salary: "))
    except ValueError:
        print("Invalid salary.")
        return

    conn = sqlite3.connect(DB_NAME)
    cursor = conn.cursor()

    cursor.execute("""
    INSERT INTO employees (name, department, salary)
    VALUES (?, ?, ?)
    """, (name, department, salary))

    conn.commit()
    conn.close()

    print("Employee added successfully.")


def view_employees():
    conn = sqlite3.connect(DB_NAME)
    cursor = conn.cursor()

    cursor.execute("SELECT * FROM employees")
    employees = cursor.fetchall()

    conn.close()

    if not employees:
        print("No employees found.")
        return

    print("\n===== Employee List =====")

    for emp in employees:
        print(
            f"ID: {emp[0]} | "
            f"Name: {emp[1]} | "
            f"Department: {emp[2]} | "
            f"Salary: ¥{emp[3]:,.0f}"
        )


def search_employee():
    keyword = input("Enter employee name: ")

    conn = sqlite3.connect(DB_NAME)
    cursor = conn.cursor()

    cursor.execute("""
    SELECT * FROM employees
    WHERE name LIKE ?
    """, ('%' + keyword + '%',))

    results = cursor.fetchall()

    conn.close()

    if not results:
        print("Employee not found.")
        return

    print("\n===== Search Results =====")

    for emp in results:
        print(
            f"ID: {emp[0]} | "
            f"Name: {emp[1]} | "
            f"Department: {emp[2]} | "
            f"Salary: ¥{emp[3]:,.0f}"
        )


def update_employee():
    try:
        employee_id = int(input("Employee ID to update: "))
    except ValueError:
        print("Invalid ID.")
        return

    conn = sqlite3.connect(DB_NAME)
    cursor = conn.cursor()

    cursor.execute(
        "SELECT * FROM employees WHERE id = ?",
        (employee_id,)
    )

    employee = cursor.fetchone()

    if not employee:
        print("Employee not found.")
        conn.close()
        return

    new_name = input(
        f"New Name ({employee[1]}): "
    ) or employee[1]

    new_department = input(
        f"New Department ({employee[2]}): "
    ) or employee[2]

    salary_input = input(
        f"New Salary ({employee[3]}): "
    )

    if salary_input:
        try:
            new_salary = float(salary_input)
        except ValueError:
            print("Invalid salary.")
            conn.close()
            return
    else:
        new_salary = employee[3]

    cursor.execute("""
    UPDATE employees
    SET name = ?, department = ?, salary = ?
    WHERE id = ?
    """, (
        new_name,
        new_department,
        new_salary,
        employee_id
    ))

    conn.commit()
    conn.close()

    print("Employee updated successfully.")


def delete_employee():
    try:
        employee_id = int(input("Employee ID to delete: "))
    except ValueError:
        print("Invalid ID.")
        return

    conn = sqlite3.connect(DB_NAME)
    cursor = conn.cursor()

    cursor.execute(
        "SELECT * FROM employees WHERE id = ?",
        (employee_id,)
    )

    employee = cursor.fetchone()

    if not employee:
        print("Employee not found.")
        conn.close()
        return

    confirm = input(
        f"Delete {employee[1]}? (y/n): "
    )

    if confirm.lower() == "y":
        cursor.execute(
            "DELETE FROM employees WHERE id = ?",
            (employee_id,)
        )

        conn.commit()
        print("Employee deleted successfully.")

    conn.close()


def main():
    create_table()

    while True:
        print("\n===== Employee Management System =====")
        print("1. Add Employee")
        print("2. View Employees")
        print("3. Search Employee")
        print("4. Update Employee")
        print("5. Delete Employee")
        print("6. Exit")

        choice = input("Choose an option: ")

        if choice == "1":
            add_employee()

        elif choice == "2":
            view_employees()

        elif choice == "3":
            search_employee()

        elif choice == "4":
            update_employee()

        elif choice == "5":
            delete_employee()

        elif choice == "6":
            print("Goodbye!")
            break

        else:
            print("Invalid option.")


if __name__ == "__main__":
    main()