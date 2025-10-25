# Quiz Management System

A quiz management system where admins create quizzes and questions, and candidates attempt quizzes and receive scores.

## Domain Overview
Actors: Admin, Candidate

Core Flows:
- Admin creates quizzes and adds questions.
- Candidate fetches randomized questions, submits answers, system computes score and stores attempts.

## Entities
### Quiz
- id: Long — system id
- title: String
- description: String
- questions: List<Question>

### Question
- id: Long
- text: String
- optionA: String
- optionB: String
- optionC: String
- optionD: String
- correctOption: String — one of A/B/C/D
- difficulty: DifficultyLevel
- quiz: Quiz

### Answer
- id: Long
- question: Question
- selectedOption: String — A/B/C/D

### CandidateEntity
- id: Long
- candidateId: String — external/public id
- name: String
- contact: String

### CandidateQuizAttempt
- id: Long
- candidate: CandidateEntity
- quiz: Quiz
- answers: List<Answer>
- score: double
- submittedAt: Instant

### Role
- Role enumeration or entity for authorization (admin/candidate)

### DifficultyLevel (enum)
- EASY
- MEDIUM
- HARD

## DTOs
### SubmitAnswersRequest
- candidateId: String
- answers: List<AnswerDTO>

### AnswerDTO
- questionId: Long
- selectedOption: String — A/B/C/D

## Repositories
### QuizRepository
- CRUD for Quiz
- Queries: by title, fetch with questions

### QuestionRepository
- CRUD for Question
- Queries: by quizId, by (quizId, difficulty), random selection with limit

### AnswerRepository
- CRUD for Answer

### CandidateRepository
- CRUD for CandidateEntity
- Query by candidateId

### CandidateQuizAttemptRepository
- CRUD for CandidateQuizAttempt
- Query by candidate.candidateId
- Optional: fetch attempts with answers and quiz

## Services
### QuizService
- Quiz createQuiz(Quiz quiz) — basic validations, persist quiz
- void addQuestionsToQuiz(Long quizId, List<Question> questions) — attach to quiz, validate options/correctness
- List<Quiz> getAllQuizzes()
- List<Question> getQuestionsByQuizId(Long quizId)
- List<Question> getQuestionByDifficulty(Long quizId, String difficulty) — maps to enum
- List<Question> getRandomQuestions(Long quizId, int limit) — random subset for candidate
- double calculateScore(Long quizId, List<SubmitAnswersRequest.Answer> answers) — compare to correctOption
- List<CandidateQuizAttempt> getAttemptsByCandidate(String candidateId)

### CandidateQuizAttemptService
- CandidateQuizAttempt saveAttempt(Long quizId, String candidateId, double score, List<SubmitAnswersRequest.Answer> answers) — create or fetch candidate, persist attempt and normalized answer rows

## Controllers
### AdminQuizController (/admin/quiz)
- POST / — create quiz
- POST /{quizId}/questions — add questions to quiz
- GET /quizzes — list quizzes
- GET /{id}/questions — list questions for quiz
- GET /{quizId}/questions/difficulty/{difficulty} — filter by difficulty

### CandidateQuizController (/candidate/quiz)
- GET /{quizId}/questions?limit=10 — randomized questions
- POST /{quizId}/submit — submit attempt via entity payload (maps to DTO internally)
- POST /{quizId}/submit-answers — submit via SubmitAnswersRequest
- GET /attempts?candidateId=... — list attempts for candidate
- Methods for candidate performance tracking.
- DOMAIN (Job Role)
    ├── IT
    ├── HR  
    └── Cybersecurity

QUIZ (Subject within Domain)
    IT Domain:
        ├── Java Quiz
        ├── OS Quiz
        ├── DSA Quiz
        └── React Quiz
    
    HR Domain:
        ├── Recruitment Quiz
        ├── Labor Law Quiz
        └── Employee Relations Quiz
    
    Cybersecurity Domain:
        ├── Network Security Quiz
        ├── Ethical Hacking Quiz
        └── VAPT Quiz

SECTION (Topics within Quiz)
    Java Quiz:
        ├── Pointers
        ├── DSA
        ├── Stack
        ├── Collections
        └── Multithreading
    
    OS Quiz:
        ├── Paging
        ├── Scheduling
        ├── Deadlock
        └── Memory Management

QUESTIONS (Within Section)
    Paging Section:
        ├── Question 1 (Easy)
        ├── Question 2 (Medium)
        ├── Question 3 (Medium)
        └── Question 4 (Hard)

