# CipherByte-Technoligies

# 1. Guess the Number Game

This Java program is an interactive "Guess the Number" game where the computer randomly selects a number between 1 and 100, and the user tries to guess it. The program provides feedback if the guess is too high or too low and tracks the number of attempts. Users have up to 10 attempts per round to guess the number. Scores are calculated based on the number of attempts taken, with higher points awarded for fewer attempts. The game supports multiple rounds, allowing users to accumulate their scores across rounds. The code also includes user-friendly prompts and proper resource management to ensure a smooth gaming experience.

# 2. Online-examination-

Creating a complete project with functionalities such as login, profile update, password update, selecting answers for MCQs, a timer with auto-submit, and session management in Java is a comprehensive task. Below is a simplified version to demonstrate these functionalities. This example will use the console for interaction and will be structured to show how these functionalities can be implemented.

Due to the complexity and length, I'll provide a basic outline of each part and a more detailed implementation of key components. 

### Project Outline
1. **User Management**: Login, profile update, password update.
2. **MCQ Handling**: Display questions, select answers.
3. **Timer and Auto-Submit**: Implement a timer that automatically submits answers.
4. **Session Management**: Handle session close and logout.

### Key Classes
1. `User`
2. `Question`
3. `MCQTest`
4. `Session`

### User Class
Manages user details and profile updates.
```java
import java.util.Scanner;

public class User {
    private String username;
    private String password;
    private String profileInfo;

    public User(String username, String password) {
        this.username = username;
        this.password = password;
        this.profileInfo = "";
    }

    public String getUsername() {
        return username;
    }

    public boolean checkPassword(String password) {
        return this.password.equals(password);
    }

    public void updateProfile(String newInfo) {
        this.profileInfo = newInfo;
        System.out.println("Profile updated successfully.");
    }

    public void updatePassword(String oldPassword, String newPassword) {
        if (checkPassword(oldPassword)) {
            this.password = newPassword;
            System.out.println("Password updated successfully.");
        } else {
            System.out.println("Old password is incorrect.");
        }
    }

    public void displayProfile() {
        System.out.println("Username: " + username);
        System.out.println("Profile Info: " + profileInfo);
    }
}
```

### Question Class
Represents an MCQ question.
```java
public class Question {
    private String question;
    private String[] options;
    private int correctAnswer; // Index of the correct answer

    public Question(String question, String[] options, int correctAnswer) {
        this.question = question;
        this.options = options;
        this.correctAnswer = correctAnswer;
    }

    public void displayQuestion() {
        System.out.println(question);
        for (int i = 0; i < options.length; i++) {
            System.out.println((i + 1) + ". " + options[i]);
        }
    }

    public boolean checkAnswer(int answer) {
        return correctAnswer == answer;
    }
}
```

### MCQTest Class
Handles the MCQ test, including timing and auto-submission.
```java
import java.util.ArrayList;
import java.util.List;
import java.util.Timer;
import java.util.TimerTask;

public class MCQTest {
    private List<Question> questions;
    private int[] userAnswers;
    private int currentQuestionIndex;
    private int score;
    private Timer timer;

    public MCQTest(List<Question> questions, int timeLimitInSeconds) {
        this.questions = questions;
        this.userAnswers = new int[questions.size()];
        this.currentQuestionIndex = 0;
        this.score = 0;

        timer = new Timer();
        timer.schedule(new TimerTask() {
            @Override
            public void run() {
                autoSubmit();
            }
        }, timeLimitInSeconds * 1000);
    }

    public void displayCurrentQuestion() {
        if (currentQuestionIndex < questions.size()) {
            questions.get(currentQuestionIndex).displayQuestion();
        } else {
            System.out.println("No more questions.");
        }
    }

    public void selectAnswer(int answer) {
        userAnswers[currentQuestionIndex] = answer;
        currentQuestionIndex++;
    }

    private void autoSubmit() {
        System.out.println("Time's up! Auto-submitting your answers.");
        calculateScore();
        displayScore();
        timer.cancel();
    }

    private void calculateScore() {
        for (int i = 0; i < questions.size(); i++) {
            if (questions.get(i).checkAnswer(userAnswers[i])) {
                score++;
            }
        }
    }

    public void displayScore() {
        System.out.println("Your score: " + score + "/" + questions.size());
    }
}
```

### Session Class
Manages the user session, including login and logout.
```java
import java.util.HashMap;
import java.util.Map;
import java.util.Scanner;

public class Session {
    private Map<String, User> users;
    private User currentUser;

    public Session() {
        users = new HashMap<>();
        // Adding a sample user
        users.put("user1", new User("user1", "password1"));
    }

    public boolean login(String username, String password) {
        if (users.containsKey(username) && users.get(username).checkPassword(password)) {
            currentUser = users.get(username);
            System.out.println("Login successful. Welcome, " + username + "!");
            return true;
        } else {
            System.out.println("Invalid username or password.");
            return false;
        }
    }

    public void logout() {
        currentUser = null;
        System.out.println("Logged out successfully.");
    }

    public User getCurrentUser() {
        return currentUser;
    }
}
```

### Main Class
Combines all components and provides a console interface.
```java
import java.util.Arrays;
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        Session session = new Session();

        // Login
        System.out.print("Enter username: ");
        String username = scanner.next();
        System.out.print("Enter password: ");
        String password = scanner.next();

        if (!session.login(username, password)) {
            return;
        }

        User user = session.getCurrentUser();

        // Update profile and password
        user.displayProfile();
        System.out.print("Do you want to update your profile? (yes/no): ");
        if (scanner.next().equalsIgnoreCase("yes")) {
            System.out.print("Enter new profile info: ");
            scanner.nextLine(); // consume the newline
            String newInfo = scanner.nextLine();
            user.updateProfile(newInfo);
        }

        System.out.print("Do you want to change your password? (yes/no): ");
        if (scanner.next().equalsIgnoreCase("yes")) {
            System.out.print("Enter old password: ");
            String oldPassword = scanner.next();
            System.out.print("Enter new password: ");
            String newPassword = scanner.next();
            user.updatePassword(oldPassword, newPassword);
        }

        // MCQ Test
        Question q1 = new Question("What is 2+2?", new String[]{"3", "4", "5"}, 2);
        Question q2 = new Question("What is the capital of France?", new String[]{"Berlin", "London", "Paris"}, 3);
        Question q3 = new Question("What is the color of the sky?", new String[]{"Blue", "Green", "Red"}, 1);
        MCQTest mcqTest = new MCQTest(Arrays.asList(q1, q2, q3), 30); // 30 seconds for the test

        while (true) {
            mcqTest.displayCurrentQuestion();
            System.out.print("Select your answer (1-3) or type -1 to submit: ");
            int answer = scanner.nextInt();
            if (answer == -1) {
                break;
            }
            mcqTest.selectAnswer(answer - 1); // Convert to zero-based index
        }

        mcqTest.displayScore();

        // Logout
        session.logout();
    }
}
```

### Summary
This Java console application simulates a user management system and an MCQ test with a timer and auto-submit functionality. It covers basic functionalities such as login, profile and password updates, MCQ handling, and session management. This code provides a foundational structure that can be expanded and integrated into a more complex system with a graphical user interface and persistent data storage.

# 3. Banky-CBTCIP
BankY: Basic Banking System Simulation BankY is a Java-based simulation of a basic banking system, developed during my internship with CipherByte Technologies. This project provides a practical implementation of fundamental banking functionalities, including account management and transactions, and serves as an excellent example of object-oriented programming and file-based data persistence.

Features Account Management: Create new accounts, view account details, and manage account information. Transactions: Perform deposits, withdrawals, and fund transfers between accounts. Persistent Storage: Utilizes Java serialization for saving and loading account data. Technologies Used Java: Core language for implementing the banking system. Serialization: For persistent data storage in files. How to Run Clone the Repository: bash Copy code git clone https://github.com/yourusername/BankY.git Compile the Code: bash Copy code javac BankY.java Run the Program: bash Copy code java BankY Getting Started Ensure Java is installed on your system. Compile and run the program as described above. Interact with the system through the command-line interface to manage bank accounts and perform transactions.
