# Quiz Proctoring System

This project is a **Quiz Proctoring System** built using **Spring Boot** and **MySQL**. It allows admins to manage quizzes, assign quizzes to students, and enables students to take quizzes with proctoring features.

---

## Features

### Admin Panel
- Login using admin credentials.
- Create, update, and delete quizzes.
- Assign specific quizzes to individual students.
- View students and their assigned quizzes.
- Monitor quiz attempts.

### Student Panel
- View quizzes assigned by admin.
- Choose a quiz to attempt.
- Take quizzes with proctoring (optional).
- Submit answers and view results.

---

## Database Structure

### Users Table
| Column   | Type      | Description            |
|----------|----------|------------------------|
| id       | BIGINT   | Primary key           |
| username | VARCHAR  | Unique username       |
| email    | VARCHAR  | Unique email          |
| password | VARCHAR  | Hashed password       |
| role     | VARCHAR  | Role (`ADMIN`/`STUDENT`) |

### Quizzes Table
| Column   | Type      | Description          |
|----------|-----------|--------------------|
| id       | BIGINT    | Primary key        |
| title    | VARCHAR   | Quiz title         |
| description | TEXT   | Quiz description   |

### Questions Table
| Column   | Type      | Description         |
|----------|-----------|-------------------|
| id       | BIGINT    | Primary key       |
| quiz_id  | BIGINT    | Foreign key to quiz|
| question | TEXT      | Question text     |
| options  | JSON      | Options           |
| answer   | VARCHAR   | Correct answer    |

### Student Quiz Assignment Table
| Column     | Type      | Description                  |
|------------|----------|-------------------------------|
| id         | BIGINT   | Primary key                  |
| student_id | BIGINT   | Foreign key to `users`       |
| quiz_id    | BIGINT   | Foreign key to `quizzes`     |
| assigned_at| DATETIME | Timestamp of assignment      |

---


