# Live Task — Java JDBC & Servlet
 
**Name:** MD Samir Tahrian
**Student ID:** IT-24007
**Department:** ICT, MBSTU
 
---
 
## Task 1: Find the n-th Prime and Save it to MySQL (JDBC)
 
The program takes `n` from the user, finds the **n-th prime number**, and saves `(n, prime)` into the `prime_numbers` table of the `testdb` database using JDBC.
 
### Tools Used
- Java (JDK 21+)
- MySQL 8.4 (Laragon) + HeidiSQL
- MySQL Connector/J 26.10.0 (`PrimeJDBC/lib/`)
- VS Code / IntelliJ IDEA
### Database Setup (SQL)
 
```sql
CREATE DATABASE IF NOT EXISTS testdb;
 
USE testdb;
 
CREATE TABLE IF NOT EXISTS prime_numbers (
    n INT PRIMARY KEY,
    prime_value BIGINT NOT NULL
);
 
SELECT * FROM prime_numbers;
```
 
![Database and table created](PrimeJDBC/SS/Screenshot%202026-11-01%20104448.png)
 
### Java Code — `PrimeJDBC/PrimeInsert.java`
 
```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.util.Scanner;
 
public class PrimeInsert {
 
    static boolean isPrime(long number) {
        if (number < 2)
            return false;
 
        for (long i = 2; i <= number / i; i++) {
            if (number % i == 0)
                return false;
        }
        return true;
    }
 
    static long nthPrime(int n) {
        int count = 0;
        long number = 1;
 
        while (count < n) {
            number++;
            if (isPrime(number))
                count++;
        }
        return number;
    }
 
    public static void main(String[] args) throws Exception {
        try (Scanner scanner = new Scanner(System.in)) {
            System.out.print("Enter n: ");
            int n = scanner.nextInt();
 
            if (n < 1) {
                System.out.println("n must be at least 1.");
                return;
            }
 
            long prime = nthPrime(n);
 
            String url = "jdbc:mysql://127.0.0.1:3306/testdb";
            String user = "root";
            String password = ""; // MySQL পাসওয়ার্ড থাকলে এখানে দিন
 
            String sql = """
                    INSERT INTO prime_numbers (n, prime_value)
                    VALUES (?, ?)
                    ON DUPLICATE KEY UPDATE prime_value = ?
                    """;
 
            try (Connection connection = DriverManager.getConnection(url, user, password);
                    PreparedStatement statement = connection.prepareStatement(sql)) {
 
                statement.setInt(1, n);
                statement.setLong(2, prime);
                statement.setLong(3, prime);
                statement.executeUpdate();
 
                System.out.println(
                        "Saved: n = " + n + ", prime = " + prime);
            }
        }
    }
}
```
 
### How to Run
 
```powershell
cd PrimeJDBC
javac PrimeInsert.java
java -cp ".;lib/*" PrimeInsert
```
 
> On Linux/macOS use `java -cp ".:lib/*" PrimeInsert`
 
### Output
 
**Program run (n = 100 → prime = 541):**
 
![Program output in terminal](PrimeJDBC/SS/Screenshot%202026-11-01%20105535.png)
 
**Data saved in MySQL:**
 
```sql
SELECT * FROM testdb.prime_numbers ORDER BY n;
```
 
| n   | prime_value |
|-----|-------------|
| 10  | 29          |
| 100 | 541         |
 
![Rows in prime_numbers table](PrimeJDBC/SS/Screenshot%202026-11-01%20110036.png)
 
---
 
## Task 2: Servlet on Apache Tomcat 10.1
 
### Hello World Servlet
URL: `http://localhost:8080/hello/hello`
 
![Hello World servlet](PrimeJDBC/SS/Screenshot%202026-11-01%20111741.png)
 
### Prime Number Checker Servlet
URL: `http://localhost:8080/hello/prime?number=<value>`
 
**Input 7 → prime:**
 
![7 is a prime number](PrimeJDBC/SS/Screenshot%202026-11-01%20112051.png)
 
**Input 4 → not prime:**
 
![4 is not a prime number](PrimeJDBC/SS/Screenshot%202026-11-01%20112208.png)
 
---
 
## Project Structure
 
![Project structure in IntelliJ IDEA](PrimeJDBC/SS/project-structure.png)
 
```
live Task/
├── .gitattributes
├── README.md
└── PrimeJDBC/
    ├── PrimeInsert.java
    ├── PrimeInsert.class
    ├── lib/
    │   └── mysql-connector-j-26.10.0.jar
    └── SS/
        └── (screenshots)
```
