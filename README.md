# Personal Cloud Storage
-- Udacity's Spring Boot Basics Course Final Project 

## Summary
Personal Cloud Storage is a web application that uses Spring MVC architecture to achieve three user-facing functionalities:

1. **Simple File Storage:** Upload/download/remove files
2. **Note Management:** Add/update/remove text notes
3. **Password Management:** Save, edit, and delete website credentials.

## Tech
+ Language: Java, SQL
+ Backend: Spring Boot, Maven
+ Frontend: HTML, CSS, Thymeleaf
+ Database: H2, MyBatis
+ Test: JUnit, Selenium

## Design
The web application is designed in the Spring MVC architecture.
![springmvc](/images/springmvc.png)

The internal files is organized under the Onion architecture.
![onion](/images/onion.png)

## Directory
![directory](/images/directory.png)

## Requirements

### The Back-End
The back-end is all about security and connecting the front-end to database data and actions. 

1. Managing user access with Spring Security
 - Restrict unauthorized users from accessing pages other than the login and signup pages. 
 - Use the security configuration to override the default login page handled by Spring Boot with custom login page.
 - Implement a custom AuthenticationProvider which authorizes user logins by matching their credentials against those stored in the database.  


2. Handling front-end calls with controllers
 - Write controllers for the application that bind application data and functionality to the front-end. That means using Spring MVC's application model to identify the templates served for different requests and populating the view model with data needed by the template. 
 - The controllers should also be responsible for determining what, if any, error messages the application displays to the user. When a controller processes front-end requests, it should delegate the individual steps and logic of those requests to other services in the application, but it should interpret the results to ensure a smooth user experience.


3. Making calls to the database with MyBatis mappers
 - Design Java classes to match the data in the database. These should be POJOs (Plain Old Java Objects) with fields that match the names and data types in the schema, and create one class per database table.
 - To connect these model classes with database data, implement MyBatis mapper interfaces for each of the model types. These mappers should have methods that represent specific SQL queries and statements required by the functionality of the application. They should support the basic CRUD (Create, Read, Update, Delete) operations for their respective models at the very least. 


### The Front-End
Develop HTML templates for the required application pages. They include fields, modal forms, success and error message elements, as well as styling and functional components using Bootstrap as a framework. Insert Thymeleaf attributes to supply the back-end data and functionality described by the following individual page requirements:

1. Login page
 - Everyone should be allowed access to this page, and users can use this page to login to the application. 
 - Show login errors, like invalid username/password, on this page. 


2. Sign Up page
 - Everyone should be allowed access to this page, and potential users can use this page to sign up for a new account. 
 - Validate that the username supplied does not already exist in the application, and show such signup errors on the page when they arise.
 - Remember to store the user's password securely!


3. Home page
The home page is the center of the application and hosts the three required pieces of functionality. The existing template presents them as three tabs that can be clicked through by the user:


 i. Files
  - The user should be able to upload files and see any files they previously uploaded.
  - The user should be able to view/download or delete previously-uploaded files.
  - Any errors related to file actions should be displayed. For example, a user should not be able to upload two files with the same name, but they'll never know unless you tell them!

 ii. Notes
  - The user should be able to create notes and see a list of the notes they have previously created.
  - The user should be able to edit or delete previously-created notes.

 iii. Credentials
 - The user should be able to store credentials for specific websites and see a list of the credentials they've previously stored. If you display passwords in this list, make sure they're encrypted!
 - The user should be able to view/edit or delete individual credentials. When the user views the credential, they should be able to see the unencrypted password.

The home page should have a logout button that allows the user to logout of the application and keep their data private.

### Testing
Combine JUnit and some simple Selenium tests to verify user-facing functionality and prove that the code is feature-complete.

1. Write tests for user signup, login, and unauthorized access restrictions.
 - Write a test that verifies that an unauthorized user can only access the login and signup pages.
 - Write a test that signs up a new user, logs in, verifies that the home page is accessible, logs out, and verifies that the home page is no longer accessible. 


2. Write tests for note creation, viewing, editing, and deletion.
 - Write a test that creates a note, and verifies it is displayed.
 - Write a test that edits an existing note and verifies that the changes are displayed.
 - Write a test that deletes a note and verifies that the note is no longer displayed.


3. Write tests for credential creation, viewing, editing, and deletion.
 - Write a test that creates a set of credentials, verifies that they are displayed, and verifies that the displayed password is encrypted.
 - Write a test that views an existing set of credentials, verifies that the viewable password is unencrypted, edits the credentials, and verifies that the changes are displayed.
 - Write a test that deletes an existing set of credentials and verifies that the credentials are no longer displayed.
 
### Password Security
Make sure not to save the plain text credentials of the application's users in the database. That's a recipe for data breach disaster! Use a hashing function to store a scrambled version instead. The class called `HashService` that can hash passwords. When the user signs up, only store a hashed version of their password in the database, and on login, hash the password attempt before comparing it with the hashed password in the database. Code sample to help illustrate the idea:

```
byte[] salt = new byte[16];
random.nextBytes(salt);
String encodedSalt = Base64.getEncoder().encodeToString(salt);
String hashedPassword = hashService.getHashedValue(plainPassword, encodedSalt);
return hashedPassword;
```

For storing credentials in the main part of the application, we can't hash passwords because it's a one-way operation. The user needs access to the unhashed password, after all! So instead, you should encrypt the passwords. The class called `EncryptionService` that can encrypt and decrypt passwords. When a user adds new credentials, encrypt the password before storing it in the database. When the user views those credentials, decrypt the password before displaying it. Code snippet on how to use `EncryptionService`:

```
SecureRandom random = new SecureRandom();
byte[] key = new byte[16];
random.nextBytes(key);
String encodedKey = Base64.getEncoder().encodeToString(key);
String encryptedPassword = encryptionService.encryptValue(password, encodedKey);
String decryptedPassword = encryptionService.decryptValue(encryptedPassword, encodedKey);
```

More about Hash Function and Encryption:

[Hash Function](https://en.wikipedia.org/wiki/Hash_function)

[Encryption](https://en.wikipedia.org/wiki/Encryption)

