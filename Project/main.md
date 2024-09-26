# Criterion A: Planning
## Problem Definition
The handling of several passwords across numerous government systems is one of the cybersecurity issues that the Japanese government is now facing. Workers often handle a variety of sensitive data, yet the current password standards have resulted in unsafe practices and inefficiencies. For example, a lot of workers are utilising risky practices, such keeping passwords in visible locations like physical notes or personal papers. This presents serious worries about possible data breaches and unauthorised access since these unsafe storage techniques expose sensitive data to hackers. In order to deal with these urgent problems, the administration is currently concentrating on bolstering security measures.

## Proposed Solution
In order to tackle the cybersecurity issues brought about by the Japanese government's poor password management practices, I suggest creating a personalised notepad application that serves as a covert password manager. This system would safely store passwords in the background while appearing to be a typical note-taking tool that employees are accustomed to using. 

Strong encryption techniques will be used in the notepad to safeguard confidential data, and it will automatically arrange and maintain passwords for several government platforms. Passwords will be easily accessible to staff members, eliminating the need for risky procedures like handwritten notes or unprotected files. This method ensures compliance with cybersecurity regulations and keeps things simple enough for government workers to utilise, all while offering a highly secure yet user-friendly solution to the problem.

## Success Criteria
1. the notepad should be able to store notes.
2. the notepad should be able to handle errors.
3. the notepad should be able to retrieve the notes based by date.
4. the notepad should change to the password manager after the master password is entered [isak2026].
5. While in password saver mode the program should be able to perform the following actions:
     * Save: the user inputs the site and the password he wants to save.
     * View: the user can see the catalogue of sites and choose one of them to decrypt with the master password and view.
     * Delete: the user can see the catalogue of sites and choose to delete one of them (only when the master password is entered).
     * Update: the user can update an old password(needs to enter both the masterpassword and the previous one).
6. the program should store all the passwords in a safe file.
7. the program should encrypt all the stored passwords(Publiv Key Encryption).
8. the program should decrypt all the stored passwords when asked.
9. the program should work for infinite amount of times.
# Criterion B: Design
### System Diagram

### Flow diagrams for algorithms

![codetoflow](https://github.com/user-attachments/assets/bb216dad-024e-45fb-a4b7-78a000fa6970)



### Data storage

In this project, **data storage** relies on the use of the `pickle` module, which allows us to serialize Python objects and save them to a file. Serialization is the process of converting an object (like a dictionary or list) into a format that can be stored and later retrieved, which is particularly useful when working with persistent data.

Here’s how we handle data storage for different components:

## 1. **Password Storage**
   - **Encryption:** Each password is encrypted using **RSA encryption** before being stored. RSA uses a pair of keys (public and private) to encrypt and decrypt the data, ensuring that even if someone accesses the stored passwords, they can't read them without the private key.
   - **Pickle for Persistence:** After encryption, the passwords (along with any metadata like the site they belong to) are saved in a dictionary. This dictionary is then serialized using `pickle` and saved to a file, usually named something like `passwords.pkl`. This allows the application to load the passwords back when needed.
   - **Security Considerations:** Since the passwords are encrypted before storage, even if the `.pkl` file is accessed by a third party, the data remains secure unless they also obtain the private key.

## 2. **Notes Storage**
   - Notes are stored as plain text within a dictionary where the keys are dates (or timestamps), and the values are the note contents. This dictionary is serialized using `pickle` and saved to a file (e.g., `notes.pkl`). Whenever you add or retrieve a note, the app loads this file, modifies or reads from it, and then resaves it.
   - **Why Pickle?** Pickle is ideal here because it allows us to store complex Python data structures (like a dictionary of notes) without having to manually convert the data into a specific format, such as JSON or CSV.

## 3. **Task Management Storage**
   - Similar to notes, tasks are stored in a dictionary, where each task has a description, due date, and other attributes. The dictionary is serialized using `pickle` and saved to a file (e.g., `tasks.pkl`).
   - Tasks can be added, viewed, and deleted by loading and modifying this file, ensuring that any tasks created during one session will still be available in the next.

## **Advantages of Pickle**
   - **Ease of Use:** Pickle makes it easy to store complex data structures without needing to manually convert them into strings or another format.
   - **Efficiency:** Since we're working within a Python application, `pickle` is a fast and efficient way to serialize and deserialize data.
   - **Custom Objects:** Pickle can handle more complex objects, like dictionaries containing encrypted data, making it more versatile than simpler storage formats like CSV.

## **Limitations and Security Considerations**
   - **Not Human-Readable:** Unlike JSON, which is human-readable, pickle files are binary, so you can't easily view or edit them manually.

By using this approach, we ensure that all sensitive data (passwords, notes, tasks) is stored persistently and securely in a format that can be easily loaded back into the application.

### Sketches of the application

<img width="629" alt="Screenshot 2024-09-26 at 4 34 43 PM" src="https://github.com/user-attachments/assets/e89d4a86-7bc9-4fb5-8a62-99bf3e2b55bb">

### Test plan

## Record of Tasks

| Task Number | Planned Action                                          | Planned Outcome                                                               | Time Estimated | Target Completion Date | Criterion |
|-------------|---------------------------------------------------------|-------------------------------------------------------------------------------|----------------|------------------------|-----------|
| 1           | 1st meeting with the client                             | Initial Client Meeting and Requirements Gathering                             | 20min          | 2024/09/13             | A         |
| 2           | Project Planning and Design                             | Development roadmap created, with client approval.                            | 30min          | 2024/09/15             | A         |
| 3           | Development Phase 1: Basic Notepad and Password Manager | First functional version of the application completed and tested.             | 1h 30min       | 2024/09/16             | B         |
| 4           | Client Review and Feedback                              | Agreement to proceed with improvements.                                       | 20min          | 2024/09/17             | B         |
| 5           | Phase 2: Adding Random Password Generator               | Successfully integrated new features and tested sound on different platforms. | 2h             | 2024/09/18             | B         |
| 6           | Meeting with Client: Review of Additional Features      | Approval to proceed with the project.                                         | 20min          | 2024/09/19             | B         |
| 7           | Final Client Delivery                                   | Project successfully delivered.                                               | 30min          | 2024/09/20             | B         |

