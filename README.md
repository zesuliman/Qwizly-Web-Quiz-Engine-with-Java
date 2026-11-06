# 🧠 Qwizly: Web Quiz Engine (Java, Spring & MySQL)

Qwizly is a lightweight, interactive web-based quiz engine for creating customized quizzes, logical puzzles, web quests, and online tests. Quiz creators start from ready-to-use templates, add questions and answers, publish the quiz, and share it with others, who take it and see their final results.

## ✨ Main Features
* Create custom, web-based quizzes.
* Edit draft quizzes to add or modify questions.
* Set the correct answer, points, and an optional explanation for each question.
* Share quizzes through a link and let quiz takers submit their answers.
* View the overall score and basic statistics for a quiz (number of attempts, average, highest, lowest, and pass rate). Advanced analytics is out of scope.

## ⚙️ Core Engineering Challenges
Items marked **(Planned)** are not implemented yet.

* **Dynamic Question Rendering:** Quiz takers see questions displayed according to the settings the creator configured, such as question order, randomization of questions and answers, and display preferences. Correct answers are never sent to the taker's browser before submission.
* **Real-Time Scoring:** Every attempt is scored on the server, and the taker's score is displayed instantly at the end of the quiz.
* **Templates and Customizable Quizzes (Planned):** Creators start from ready-to-use templates and add questions, hints, and answers while the quiz is a draft. Published quizzes are locked; to change one, duplicate it into a new draft.
* **AI-Assisted Question Generation (Planned):** Creators provide their own sources and review the AI-generated questions and answers before adding them to the quiz.
* **Self-Paced and Live Modes (Planned):** Quizzes can be taken at each taker's own pace (for example, in a marketing campaign) or run live, with questions released in real time to all connected takers.
* **Link-Based Distribution (Planned):** Creators share a quiz through a link that they can send by email or WhatsApp. WhatsApp only delivers the link; the quiz is always taken in the web app.
* **Quiz Taker Verification (Planned):** Takers verify themselves with a one-time code sent to their email or WhatsApp number, with no account needed.
* **Concurrency and Integrity (Planned):** Server-enforced timers, attempt limits, and protection against duplicate submissions.
* **Scalability (Planned):** Designed to support 1,000 simultaneous quiz takers.
* **Responsive Design:** Works smoothly on both desktop and mobile browsers.
* **Role Management:** Quiz creators can only access, modify, or delete the quizzes they created. Quiz takers can only answer quizzes they have received an invitation link to.

## 🛠️ Tech Stack
* **Frontend:** HTML, Bootstrap, React
* **Backend:** Java, Spring Boot, Spring Security, built as a modular monolith (REST API; WebSocket for live mode is planned)
* **Database:** MySQL
* **Caching and live state:** Redis (planned, for live sessions across multiple instances)
* **AI:** Third-party LLM API for question generation (planned)
* **Messaging:** Email and WhatsApp Business Platform for sending quiz links and one-time codes (planned)
* **File storage:** AWS S3 for uploaded sources (planned)
* **Deployment:** Docker and Docker Compose locally; AWS in production (planned)

## 🚀 Getting Started

### Prerequisites
Make sure you have **Docker** and **Docker Compose** installed on your local machine.

### Installation
The easiest way to try the app is with Docker:
1. Download the `docker-compose.yml` file provided in the repository.
2. Open a terminal, navigate to the folder containing the file, and run:
   ```bash
   docker-compose up
   ```
3. Docker automatically downloads the correct version of MySQL, initializes the database schema, installs the Java dependencies, and launches the application.

## 💡 How to Tweak This Project for Your Own Uses
This is an open-source project, so feel free to clone and rename the repository for your own purposes. It is a great starter boilerplate for building secure, data-driven web applications.

## 🐛 Found a Bug?
If you find an issue or have an improvement to suggest, please open an issue in the Issues tab above. If you would like to submit a pull request (PR) with a fix, please reference the issue you created.

## 🚧 Known Issues (Work in Progress)
The UI is not complete yet. I am currently polishing the remaining web pages.

## ☕ Like this project?
If you find this project helpful, feel free to [buy me a coffee](https://buymeacoffee.com/zeinab.ibrahim?new=1)!
