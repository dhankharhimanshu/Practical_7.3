Part C: Student Management System Using JDBC and MVC
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
