# Quiz Proctoring System

This project is a **Quiz Proctoring System** built using **Spring Boot** and **MySQL**. It allows admins to manage quizzes, assign quizzes to students, and enables students to take quizzes with proctoring features.

---
## Entity Model & Relationships

### 1️⃣ User
Represents both **Admins** and **Students** who interact with the Quiz System.

**Attributes**
- `id` — Unique identifier for the user (auto-generated)
- `username` — User’s display name
- `email` — Unique email used for login
- `password` — User password (stored as plain text or encoded)
- `role` — Defines user type (`ADMIN` / `STUDENT`)

**Behavior**
- **Admin:** Can create quizzes and view results.
- **Student:** Can attempt assigned quizzes and view their scores.

---

### 2️⃣ Quiz
Represents an individual quiz created by an **Admin**.

**Attributes**
- `id` — Unique quiz identifier
- `title` — Name of the quiz
- `duration` — Total time to complete (in minutes)
- `questions` — List of `Question` entities linked via OneToMany
- `quizResults` — Collection of results submitted for this quiz

**Relationships**
- One Quiz → Many Questions  
- One Quiz → Many QuizResults

**Purpose**
- Serves as the central entity connecting `Question`, `QuizResult`, and `QuizSubmission`.

---

### 3️⃣ Question
Represents a single question belonging to a Quiz.

**Attributes**
- `id` — Unique question ID
- `text` — The actual question
- `optionA`, `optionB`, `optionC` — Multiple choice options
- `correctAnswer` — One character (‘A’, ‘B’, or ‘C’) representing the correct option
- `quiz` — `ManyToOne` relationship linking to the parent Quiz

**Relationships**
- Many Questions → One Quiz

**Purpose**
- Defines all questions linked to each quiz.
- Used during quiz creation (by Admin) and quiz-taking (by Student).

---

### 4️⃣ QuizSubmission
Used to capture the student’s answers when attempting a quiz before evaluation.

**Attributes**
- `quizId` — ID of the quiz being attempted
- `answers` — A map of question IDs to selected options (`{QuestionId: 'A'}`)

**Purpose**
- Acts as a data transfer object (not persisted in DB).
- Used to submit quiz answers from the frontend → backend.

---

### 5️⃣ QuizResult
Stores the final score for each quiz attempt after evaluation.

**Attributes**
- `id` — Unique identifier
- `user` — Student who attempted the quiz
- `quiz` — Quiz attempted
- `score` — Calculated based on correct answers

**Relationships**
- Many QuizResults → One User  
- Many QuizResults → One Quiz

**Purpose**
- Stores evaluated results for each student.
- Enables both Admin and Student to view past performance.

---

### 6️⃣ UserDto
A lightweight Data Transfer Object used during registration or login.

**Attributes**
- `username`
- `email`
- `password`

**Purpose**
- Used to capture user input during signup/login before creating a `User` entity.
- Prevents exposure of sensitive fields in APIs.

---

## ⚙️ Entity Relationships Overview

| From Entity | Relationship | To Entity | Type |
|--------------|--------------|------------|------|
| `User` | 1 → * | `QuizResult` | OneToMany |
| `Quiz` | 1 → * | `Question` | OneToMany |
| `Quiz` | 1 → * | `QuizResult` | OneToMany |
| `Question` | * → 1 | `Quiz` | ManyToOne |
| `QuizResult` | * → 1 | `User`, `Quiz` | ManyToOne |

---
