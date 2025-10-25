Part C: Student Management System Using JDBC and MVC


import java.sql.*;
import java.util.*;

// ========================== PART A: BASIC JDBC CONNECTION ==========================
class JDBCConnectionDemo {
    public static void fetchEmployeeData() {
        String url = "jdbc:mysql://localhost:3306/testdb"; // change database name
        String user = "root"; // your username
        String pass = "password"; // your password

        try (Connection con = DriverManager.getConnection(url, user, pass);
             Statement st = con.createStatement();
             ResultSet rs = st.executeQuery("SELECT EmpID, Name, Salary FROM Employee")) {

            System.out.println("=== Employee Table Data ===");
            while (rs.next()) {
                System.out.println(rs.getInt("EmpID") + " | " +
                                   rs.getString("Name") + " | " +
                                   rs.getDouble("Salary"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}

// ========================== PART B: CRUD ON PRODUCT TABLE ==========================
class ProductCRUD {
    private static final String URL = "jdbc:mysql://localhost:3306/testdb";
    private static final String USER = "root";
    private static final String PASS = "password";

    private Connection connect() throws SQLException {
        return DriverManager.getConnection(URL, USER, PASS);
    }

    // Insert
    public void addProduct(int id, String name, double price, int qty) {
        String sql = "INSERT INTO Product(ProductID, ProductName, Price, Quantity) VALUES (?, ?, ?, ?)";
        try (Connection con = connect();
             PreparedStatement ps = con.prepareStatement(sql)) {
            con.setAutoCommit(false);
            ps.setInt(1, id);
            ps.setString(2, name);
            ps.setDouble(3, price);
            ps.setInt(4, qty);
            ps.executeUpdate();
            con.commit();
            System.out.println("Product Added Successfully!");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    // Read
    public void viewProducts() {
        String sql = "SELECT * FROM Product";
        try (Connection con = connect();
             Statement st = con.createStatement();
             ResultSet rs = st.executeQuery(sql)) {
            System.out.println("=== Product List ===");
            while (rs.next()) {
                System.out.println(rs.getInt("ProductID") + " | " +
                                   rs.getString("ProductName") + " | " +
                                   rs.getDouble("Price") + " | " +
                                   rs.getInt("Quantity"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    // Update
    public void updateProductPrice(int id, double newPrice) {
        String sql = "UPDATE Product SET Price=? WHERE ProductID=?";
        try (Connection con = connect();
             PreparedStatement ps = con.prepareStatement(sql)) {
            con.setAutoCommit(false);
            ps.setDouble(1, newPrice);
            ps.setInt(2, id);
            int rows = ps.executeUpdate();
            if (rows > 0) System.out.println("Product Updated Successfully!");
            con.commit();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    // Delete
    public void deleteProduct(int id) {
        String sql = "DELETE FROM Product WHERE ProductID=?";
        try (Connection con = connect();
             PreparedStatement ps = con.prepareStatement(sql)) {
            con.setAutoCommit(false);
            ps.setInt(1, id);
            int rows = ps.executeUpdate();
            if (rows > 0) System.out.println("Product Deleted Successfully!");
            con.commit();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    // Menu-driven interface
    public void showMenu() {
        Scanner sc = new Scanner(System.in);
        while (true) {
            System.out.println("\n=== Product CRUD Menu ===");
            System.out.println("1. Add Product");
            System.out.println("2. View Products");
            System.out.println("3. Update Product Price");
            System.out.println("4. Delete Product");
            System.out.println("5. Exit");
            System.out.print("Enter Choice: ");
            int ch = sc.nextInt();

            switch (ch) {
                case 1:
                    System.out.print("Enter ID: "); int id = sc.nextInt();
                    sc.nextLine();
                    System.out.print("Enter Name: "); String name = sc.nextLine();
                    System.out.print("Enter Price: "); double price = sc.nextDouble();
                    System.out.print("Enter Quantity: "); int qty = sc.nextInt();
                    addProduct(id, name, price, qty);
                    break;
                case 2: viewProducts(); break;
                case 3:
                    System.out.print("Enter ProductID: "); int pid = sc.nextInt();
                    System.out.print("Enter New Price: "); double p = sc.nextDouble();
                    updateProductPrice(pid, p);
                    break;
                case 4:
                    System.out.print("Enter ProductID: "); int did = sc.nextInt();
                    deleteProduct(did);
                    break;
                case 5:
                    System.out.println("Exiting...");
                    return;
                default:
                    System.out.println("Invalid Choice!");
            }
        }
    }
}

// ========================== PART C: STUDENT MANAGEMENT (MVC) ==========================

// --- Model ---
class Student {
    int studentID;
    String name;
    String department;
    double marks;

    Student(int studentID, String name, String department, double marks) {
        this.studentID = studentID;
        this.name = name;
        this.department = department;
        this.marks = marks;
    }

    public String toString() {
        return studentID + " | " + name + " | " + department + " | " + marks;
    }
}

// --- Controller ---
class StudentController {
    private static final String URL = "jdbc:mysql://localhost:3306/testdb";
    private static final String USER = "root";
    private static final String PASS = "password";

    private Connection connect() throws SQLException {
        return DriverManager.getConnection(URL, USER, PASS);
    }

    // CREATE
    public void addStudent(Student s) {
        String sql = "INSERT INTO Student(StudentID, Name, Department, Marks) VALUES (?, ?, ?, ?)";
        try (Connection con = connect();
             PreparedStatement ps = con.prepareStatement(sql)) {
            ps.setInt(1, s.studentID);
            ps.setString(2, s.name);
            ps.setString(3, s.department);
            ps.setDouble(4, s.marks);
            ps.executeUpdate();
            System.out.println("Student Added Successfully!");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    // READ
    public void viewStudents() {
        String sql = "SELECT * FROM Student";
        try (Connection con = connect();
             Statement st = con.createStatement();
             ResultSet rs = st.executeQuery(sql)) {
            System.out.println("=== Student Records ===");
            while (rs.next()) {
                System.out.println(rs.getInt("StudentID") + " | " +
                                   rs.getString("Name") + " | " +
                                   rs.getString("Department") + " | " +
                                   rs.getDouble("Marks"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    // UPDATE
    public void updateMarks(int id, double marks) {
        String sql = "UPDATE Student SET Marks=? WHERE StudentID=?";
        try (Connection con = connect();
             PreparedStatement ps = con.prepareStatement(sql)) {
            ps.setDouble(1, marks);
            ps.setInt(2, id);
            ps.executeUpdate();
            System.out.println("Student Marks Updated!");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    // DELETE
    public void deleteStudent(int id) {
        String sql = "DELETE FROM Student WHERE StudentID=?";
        try (Connection con = connect();
             PreparedStatement ps = con.prepareStatement(sql)) {
            ps.setInt(1, id);
            ps.executeUpdate();
            System.out.println("Student Deleted!");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}

// --- View ---
class StudentView {
    public void showMenu(StudentController controller) {
        Scanner sc = new Scanner(System.in);
        while (true) {
            System.out.println("\n=== Student Management Menu ===");
            System.out.println("1. Add Student");
            System.out.println("2. View Students");
            System.out.println("3. Update Marks");
            System.out.println("4. Delete Student");
            System.out.println("5. Exit");
            System.out.print("Enter Choice: ");
            int ch = sc.nextInt();

            switch (ch) {
                case 1:
                    System.out.print("Enter ID: "); int id = sc.nextInt();
                    sc.nextLine();
                    System.out.print("Enter Name: "); String name = sc.nextLine();
                    System.out.print("Enter Department: "); String dept = sc.nextLine();
                    System.out.print("Enter Marks: "); double marks = sc.nextDouble();
                    controller.addStudent(new Student(id, name, dept, marks));
                    break;
                case 2: controller.viewStudents(); break;
                case 3:
                    System.out.print("Enter StudentID: "); int sid = sc.nextInt();
                    System.out.print("Enter New Marks: "); double newMarks = sc.nextDouble();
                    controller.updateMarks(sid, newMarks);
                    break;
                case 4:
                    System.out.print("Enter StudentID: "); int did = sc.nextInt();
                    controller.deleteStudent(did);
                    break;
                case 5:
                    System.out.println("Exiting...");
                    return;
                default:
                    System.out.println("Invalid Choice!");
            }
        }
    }
}

// ========================== MAIN PROGRAM ==========================
public class JDBCApplication {
    public static void main(String[] args) {
        System.out.println("=== JDBC & MVC Demonstration ===");

        // --- Part A ---
        JDBCConnectionDemo.fetchEmployeeData();

        // --- Part B ---
        ProductCRUD crud = new ProductCRUD();
        // crud.showMenu(); // Uncomment to run interactive CRUD menu

        // --- Part C ---
        StudentController controller = new StudentController();
        StudentView view = new StudentView();
        // view.showMenu(controller); // Uncomment to run MVC student management
    }
}


1. Model — Student.java
package model;

public class Student {
    private int studentID;
    private String name;
    private String department;
    private double marks;

    public Student(int studentID, String name, String department, double marks) {
        this.studentID = studentID;
        this.name = name;
        this.department = department;
        this.marks = marks;
    }

    public int getStudentID() { return studentID; }
    public String getName() { return name; }
    public String getDepartment() { return department; }
    public double getMarks() { return marks; }
}

⚙️ 2. Controller — StudentDAO.java
package controller;
import java.sql.*;
import model.Student;

public class StudentDAO {
    Connection con;

    public StudentDAO() throws Exception {
        Class.forName("com.mysql.cj.jdbc.Driver");
        con = DriverManager.getConnection("jdbc:mysql://localhost:3306/schooldb", "root", "password");
    }

    public void addStudent(Student s) throws SQLException {
        PreparedStatement ps = con.prepareStatement("INSERT INTO Student VALUES (?, ?, ?, ?)");
        ps.setInt(1, s.getStudentID());
        ps.setString(2, s.getName());
        ps.setString(3, s.getDepartment());
        ps.setDouble(4, s.getMarks());
        ps.executeUpdate();
        System.out.println("✅ Student added successfully!");
    }

    public void viewAll() throws SQLException {
        Statement st = con.createStatement();
        ResultSet rs = st.executeQuery("SELECT * FROM Student");
        System.out.println("ID\tName\tDept\tMarks");
        while (rs.next()) {
            System.out.println(rs.getInt(1) + "\t" + rs.getString(2) + "\t" +
                               rs.getString(3) + "\t" + rs.getDouble(4));
        }
    }

    public void updateMarks(int id, double marks) throws SQLException {
        PreparedStatement ps = con.prepareStatement("UPDATE Student SET Marks=? WHERE StudentID=?");
        ps.setDouble(1, marks);
        ps.setInt(2, id);
        ps.executeUpdate();
        System.out.println("✅ Marks updated!");
    }

    public void deleteStudent(int id) throws SQLException {
        PreparedStatement ps = con.prepareStatement("DELETE FROM Student WHERE StudentID=?");
        ps.setInt(1, id);
        ps.executeUpdate();
        System.out.println("🗑️ Student deleted!");
    }
}

3. View — StudentApp.java
package view;
import java.util.*;
import model.Student;
import controller.StudentDAO;

public class StudentApp {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        try {
            StudentDAO dao = new StudentDAO();
            while (true) {
                System.out.println("\n=== STUDENT MENU ===");
                System.out.println("1. Add Student");
                System.out.println("2. View All Students");
                System.out.println("3. Update Marks");
                System.out.println("4. Delete Student");
                System.out.println("5. Exit");
                System.out.print("Enter choice: ");
                int ch = sc.nextInt();

                switch (ch) {
                    case 1:
                        System.out.print("Enter ID, Name, Dept, Marks: ");
                        int id = sc.nextInt();
                        String name = sc.next();
                        String dept = sc.next();
                        double marks = sc.nextDouble();
                        dao.addStudent(new Student(id, name, dept, marks));
                        break;
                    case 2:
                        dao.viewAll();
                        break;
                    case 3:
                        System.out.print("Enter ID and new Marks: ");
                        int sid = sc.nextInt();
                        double newMarks = sc.nextDouble();
                        dao.updateMarks(sid, newMarks);
                        break;
                    case 4:
                        System.out.print("Enter ID to delete: ");
                        int delId = sc.nextInt();
                        dao.deleteStudent(delId);
                        break;
                    case 5:
                        System.out.println("Exiting...");
                        return;
                    default:
                        System.out.println("Invalid choice!");
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            sc.close();
        }
    }
}
