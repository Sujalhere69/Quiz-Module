# Quiz Proctoring System

This project is a **Quiz Proctoring System** built using **Spring Boot** and **MySQL**. It allows admins to manage quizzes, assign quizzes to candidates, and enables candidates to take quizzes with proctoring features.

---

## Entity Model & Relationships

### 1️⃣ User
Represents both **Admins** and **Candidates** who interact with the Quiz System.

**Attributes**
- `id` — Unique identifier for the user (auto-generated)
- `username` — User’s display name
- `email` — Unique email used for login
- `password` — User password (stored as plain text or encoded)
- `role` — Defines user type (`ADMIN` / `CANDIDATE`)

**Behavior**
- **Admin:** Can create quizzes and view results.
- **Candidate:** Can attempt assigned quizzes and view their scores.

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
- Used during quiz creation (by Admin) and quiz-taking (by Candidate).

---

### 4️⃣ QuizSubmission
Used to capture the candidate’s answers when attempting a quiz before evaluation.

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
- `user` — Candidate who attempted the quiz
- `quiz` — Quiz attempted
- `score` — Calculated based on correct answers

**Relationships**
- Many QuizResults → One User  
- Many QuizResults → One Quiz

**Purpose**
- Stores evaluated results for each candidate.
- Enables both Admin and Candidate to view past performance.

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

## Services that interact with entities

### 1. QuestionService

**Description**  
Manages all question-related business logic such as creating, updating, retrieving, and deleting questions.

**Methods**

| Method | Purpose |
|--------|---------|
| `saveQuestion(Question question)` | Saves a question entity. |
| `findQuestionById(Long id)` | Retrieves a question by ID. |
| `updateQuestion(Long id, Question details)` | Updates an existing question. |
| `deleteQuestion(Long id)` | Deletes a question by ID. |

**Responsibilities**
- Validate existence before update or delete.
- Throw `IllegalArgumentException` if the question doesn't exist.
- Delegate persistence to `QuestionRepository`.

---

### 2. UserService

**Description**  
Responsible for managing users, including registration and retrieval of the logged-in user.

**Methods**

| Method | Purpose |
|--------|---------|
| `registerNewUser(UserDto userDto)` | Converts `UserDto` to `User`, assigns default role, and saves. |
| `getAuthenticatedUser()` | Fetches the currently logged-in user via Spring Security. |

**Responsibilities**
- Manage user creation with default user role.
- Retrieve authenticated user details handling possible exceptions.
- Interface with `UserRepository` for persistence.

---

### 3. QuizService

**Description**  
Manages quiz and quiz result operations including creation, retrieval, deletion, and evaluation support.

**Methods**

| Method | Description |
|--------|-------------|
| `saveQuiz(Quiz quiz)` | Saves or updates a Quiz entity. |
| `getAllQuizzes()` | Returns a list of all quizzes. |
| `getQuizById(Long id)` | Retrieves a quiz by its ID; throws if not found. |
| `deleteQuiz(Long id)` | Deletes a quiz entity by ID. |
| `getCorrectAnswers(Long quizId)` | Returns a map of question IDs to the correct answers for a quiz. |
| `saveQuizResult(User user, Quiz quiz, int score)` | Saves a candidate's quiz attempt along with the obtained score. |

**Responsibilities**
- Save quiz attempt results through `QuizResultRepository`.
- Validate quiz existence on retrieval.

---

## Repositories

### UserRepository
- Data access layer for User entity operations.
- Extends JpaRepository for CRUD operations.
- Custom query methods for username and email lookups.

### QuizRepository
- Data access layer for Quiz entity operations.
- Extends JpaRepository for quiz management.
- Supports quiz retrieval and filtering.

### QuestionRepository
- Data access layer for Question entity operations.
- Extends JpaRepository for question management.
- Custom methods for quiz-specific question queries.

### QuizResultRepository
- Data access layer for QuizResult entity operations.
- Extends JpaRepository for result storage and retrieval.
- Methods for candidate performance tracking.
