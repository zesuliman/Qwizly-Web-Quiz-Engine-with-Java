# 🧠 Web Quiz Engine (Java, Spring & MySQL)

## This project is a lightweight, interactive web-based quiz engine that allows users to generate customized questions, logical puzzles, web quests, or online tests. 
Users can create quiz templates, populate them with questions and answers, publish the quiz, and share it with others to take and view their final results.

## ✨ Main Features
* Create custom, web-based quizzes.
* Edit existing quizzes to add or modify questions.
* Specify the correct answer and logic for each question.
* Securely share quizzes with other users and allow them to submit answers.
* Calculate the overall score and basic statistics (number of attempts, average, highest, lowest, and pass rate) for a specific quiz. Advanced analytics is out of scope.

## ⚙️ Core Engineering Challenges
* **Dynamic Question Rendering:** Automatically loads questions based on the configuration or display preferences set by the creator.
* **Real-Time Scoring:** Calculates and displays the user's score instantly at the end of the quiz.
* **Customizable Quiz Banks:** Easily add new questions, hints, and answers during or after creation.
* **Responsive Design:** Works smoothly on both desktop and mobile browsers.
* **Role Management:** Quiz Creators can only access, modify, or delete the quizzes they created. Quiz Takers can only answer quizzes they have received an invitation link to.

## 🛠️ Tech Stack
* **Frontend:** HTML, Bootstrap, React
* **Backend:** Java, Spring Boot, Spring Security
* **Database:** MySQL
* **Deployment:** Docker
  
### Prerequisites
Make sure you have **Docker** and **Docker Compose** installed on your local machine.

### Installation
The easiest way to see how this works is to launch the app instantly using Docker:
1. Download the `docker-compose.yml` file provided in the repository.
2. Open your terminal, navigate to the folder containing the file, and run:
   ```bash
   docker-compose up
   ```
3. Docker will automatically download the correct version of MySQL, initialize the database schema, install the Java dependencies, and launch the application.

💡 How to Tweak This Project for Your Own Uses
Since this is an open-source project, I encourage you to clone and rename this repository to use for your own purposes. It is a great starter boilerplate for building secure, data-driven web applications!

🐛 Find a Bug?
If you find an issue or would like to submit an improvement to this project, please submit an issue using the Issues tab above. If you would like to submit a Pull Request (PR) with a fix, please reference the issue you created!

🚧 Known Issues (Work in Progress)
The UI is not entirely complete. I am currently working on polishing the remaining web pages.

☕ Like this project?
If you find this project helpful and are feeling generous,  [buy me a coffee!](https://buymeacoffee.com/zeinab.ibrahim?new=1) 
