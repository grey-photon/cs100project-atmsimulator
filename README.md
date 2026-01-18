# cs100project-atmsimulator
ATM Account Simulator (not an ATM) for my first ever CS-100 project

ATM Account Simulator
Features implemented:
-	All the features listed in the project ideas pdf have been implemented (including additional features). Please refer to the feature set from there.

General notes:
-	All files are modified in /build, source files are not modified when the executable is run
-	Limits regarding integers are configured in the GUI files, preventing integers with greater than 9 digits to be entered, hence no checking of this in main.cpp
-	The code writes data back to the data files every time data in the vectors is modified (to prevent data losses in case of system or power failure)
-	TGUI & SFML are used for the GUI
-	1066x600 resolution used for easy compatibility with lower resolution displays
-	Constants are declared at the top of the file to allow for easy modification of key features
-	By default, the number of errors before an account is locked is 3, and the default daily withdrawal limit is 10,000
-	Account locks only last for that session. To clear an account lock, restart the program
-	Usernames and pins must pass validation before a user can be created, this also keeps usernames unique, so they are usable as identifiers.
-	Some users already exist in the assetdata. You can simply empty the files to remove them and their transactions. If you would like to use these users, you may 

Programming concepts used:
-	GUI, File Handling, Recursion, Classes, Structs, Vectors, Pointers, Strings.

Basic program structure (in logical order):
1.	Constants declared (Key features, Data file paths, GUI file paths)
2.	Classes declared (User and Transaction)
3.	Global variables declared (Vectors, Objects, Pointers, Counters, Flags)
4.	Text file I/O functions declared (Helper, User data, Transaction data)
5.	Vector Search functions declared (User vector and Transaction vector)
6.	Program logic/data manipulation functions declared (Helpers, Validators, User vector modification, Transaction vector modification, Transaction vector reading)
7.	GUI display functions declared (each function displays a different menu/screen).

Program Flow (simplified):
1.	Constants and Global Variables declared
2.	Call functions to read data files
3.	Render a window (1066x600)
4.	Display an initial screen, which can result in either a or b:
a.	If there was an error reading the data files, an error popup is shown and the elements on the main window are disabled. When the popup is closed, the program Is terminated.
b.	If files were read successfully, the user can operate the program as intended.
5.	Loop which allows TGUI to handle events occurring on the GUI.

Code description (data structures and functions):
-	Constants for key features set
-	User class created with the attributes: username, pin, balance, dailylimit, usedlimit, lockstatus and relevant methods.
-	Transaction struct created with attributes: username, transactionvalue, transactiondate
-	Uservector and Transactionvector created in global scope
-	Currentuser pointer created, which initially points at the blank user object, blankuserobject
-	Count variable to keep track of the error count and a Bool flag to keep track of if the day has changed are created
Text File I/O Functions:
-	stringCommaParser() is a helper function that takes a string as a parameter and updates an array with elements of the string given to the function (the string array and a variable to keep track of the index are passed as parameters by reference). The separation symbol is a comma. Suppose you have the string “username2,1111,1000”. If this string is given to stringCommaParser(), it will update the array parameter so that the first index will contain “username2”, the next will contain “1111” and the last will contain “1000” and the size variable will be updated to 2 (as the elements are at 0,1,2).
-	readUserData() is a function which opens the user data file, reads the first line (containing the date of when the program was last run) and compares in with the current date, then updates the daychanged variable accordingly. After this it loops and reads each line of text, using stringCommaParser() to separate the elements, and then writes this data to a user object which is appended to the user vector, closing the file when the last line is read. If the file fails to open, it returns false, if reading was successful, it returns true.
-	readTransactionData() is a function which opens the transaction data file, it loops and reads each line of text, using stringCommaParser() to separate the elements, and then writes this data to a transaction object which is appended to the transaction vector, closing the file when the last line is read. If the file fails to open, it returns false, if reading was successful, it returns true.
-	writeUserData() is a function which opens the user data file, writes the current date on the first line, and then loops through the user vector and writes the contents of the user objects at each index back onto the file in the same format they were read. If the file fails to open, it throws an error using cerr (which is not visible in WIN32 applications, but if recompiled normally, the error can be seen in the terminal).
-	writeTransactionData() is a function which opens the transaction data file and loops through the transaction vector and writes the contents of the user objects at each index back onto the file in the same format they were read. If the file fails to open, it throws an error using cerr (which is not visible in WIN32 applications, but if recompiled normally, the error can be seen in the terminal).
Vector Search Functions:
-	searchUserVector() searches the user vector for a username (given as a parameter). If the username is found, it returns its index in the vector. If not, it returns -1.
-	searchTransactionVector() searches the transaction vector for a username (given as a parameter). Updates an array with all the indexes where the username was found in the vector and increments the size variable accordingly (the integer array and a variable to keep track of the index are passed as parameters by reference)
Program Logic (Data Manipulation) Functions:
-	setWidthOfString() performs a similar function to setw(), but the difference is that this function returns the formatted string (so it can be stored in a variable as well, which setw() cannot do). It takes the string to format and the width as parameter. Behaviour is identical to setw() using whitespaces to pad the string.
-	recursiveIntegerValidation() is a recursive function which checks if the parameter string contains only digits (0-9). If it does, it returns true. If not, it returns false.
-	validatePin() uses recursiveIntegerValidation() to validate a pin given as a parameter. It has the added feature to validating the length of the pin (exactly 4 digits), which recursiveIntegerValidation() does not have the capability of doing
-	validateUsername() validates a username given as a parameter. Check that the number of characters is between 5 and 17, and that all characters are alphanumeric (either letters or digits, no symbols)
-	createUser() takes a username, a pin and a confirmation pin as parameters, then validates all of these (using validatePin() and validateUsername(), along with other logic) and finally creates a user which is added to the user vector if the username is not already present (checked using searchUserVector()). The user data file is updated every time the user vector is modified. Returns error codes if checks fail: 0 = user inserted into vector successfully, 1 = username taken, 2 = invalid username, 3 = invalid pin, 4 = pins do not match.
-	userLogin() takes a username and a pin as parameters. It validates these (using validatePin() and validateUsername()), then checks if the account is locked and finally updates currentpointer to point at the location where the given username is stored in the user vector if the username exists (checked using searchUserVector()). If an incorrect pin is entered a number of times equal to the constant defined at the start of the code, the account will be locked for the session (by changing the flag for that user). Returns error codes if checks fail: 0 = user logged in successfully, 1 = invalid username, 2 = invalid pin, 3 = username doesn’t exist, 4 = too many failed attempts, account locked, 5 = incorrect pin.
-	deleteUser() takes a username, a pin and a confirmdelete phrase as parameters. It then attempts to log in the user (using userLogin()), then checks if the confirmdelete phrase is correct before deleting the user objects from the user vector. Returns error codes if checks fail: 0 = user deleted successfully, 1 = invalid username, 2 = invalid pin, 3 = username doesn’t exist, 4 = too many failed attempts, account locked, 5 = incorrect pin, 6 = internal error deleting user (for debugging), 7 = confirmdelete phrase incorrect.
-	createTransaction() searches the transaction vector (using searchTransactionVector()), if there are 5 or more transactions, it deletes the oldest transaction (as the project is designed to maintain the last 5 transactions per user). It then creates a new transaction object with the current user’s username, a transaction value (passed as a parameter) and a timestamp (made by ctime() and then formatted). Finally, it appends this object to the transaction vector, and updates the transaction data file.
-	fiveRecentTransactions() finds (up to) the last 5 transactions made by the current user (using searchTransactionVector()), then formats these and returns a string which contains the last 5 transactions, aligned and numbered with each on a new line.
-	changePin() takes a current pin, a new pin and a confirmation pin as parameters, then validates all of these (using validatePin(), along with other logic) and modifies the current user’s pin if all checks are passed. The user data file is updated every time the user vector is modified. Returns error codes if checks fail: 0 = pin changed successfully, 1 = invalid current pin, 2 = invalid new pin, 3 = new pins (new and confirm) do not match, 4 = incorrect current pin entered.
GUI Display Functions:
-	loadNewDisplay() is a helper function which clears all widgets and loads the new ones from a file path (given as a parameter).
-	displayAccountManagement() loads the accountmanagement gui file, displays an error if data files were not read successfully and terminates. If files were read successfully, allows the user to proceed with the program. Shows a confirmation popup when the user presses the quit button.
-	displayCreateUser() loads the createuser gui file. Shows error messages based on createUser() if fields have issues. Shows a confirmation popup when the account is created successfully and then moves the user to the login screen.
-	
-	displayLoginUser() loads the userlogin gui file. Shows error messages based on userLogin() if fields have issues. When login is successful, moves the user to the logged in screen.
-	displayDeleteUser() loads the deleteuser gui file. Shows error messages based on deleteUser() if fields have issues. Shows a confirmation popup when the account is delete successfully and then returns the user to the delete user screen.
-	displayLoggedIn() loads the loggedin gui file. Different buttons can be pressed which lead to different screens.
-	displayDeposit() loads the depositwithdraw gui file. Sets elements for the a deposit screen. Validates the deposit (using recursiveIntegerValidation, also ensuring the deposit value is valid and that depositing such a value will not overload the integer data type). If any check fails, displays an error message. Displays a popup if deposit successful, then returns the user to the logged in screen.
-	displayWithdraw() loads the depositwithdraw gui file. Sets elements for the a withdraw screen. Validates the withdrawal (using recursiveIntegerValidation, also ensuring the withdraw value is valid, that the daily limit has not been reached and that sufficient funds are available). If any check fails, displays an error message. Displays a popup if withdrawal successful, then returns the user to the logged in screen.
-	displayCurrentBalance() loads the currentbalance gui file. Displays the user’s current balance (obtained via a get method on the current user pointer).
-	displaySettings() loads the usersettings gui file. Different buttons can be pressed which lead to different screens.
-	displayRecentTransactions() loads the recenttransactions gui file. Shows the last 5 transactions made by the user currently logged in (using fiveRecentTransactions()).
-	displayCheckWithdrawalLimit() loads the checkwithdrawallimit gui file. Displays the current user’s daily limit and the amount they have used up today (obtained via get methods).
-	displaySetWithdrawalLimit() loads the setwithdrawallimit gui file. Validates the new limit entered by the user (using recursiveIntegerValidation()). Displays an error message if the limit entered is invalid. Displays a popup if the limit was changed successfully, then brings the user back to the checkwithdrawallimit screen.
-	displayChangePin() loads the changepin gui file. Uses changePin() to validate and change the pin. Returns error messages based on error codes if changing the pin fails. If successful, displays a popup, then returns the user to the usersettings screen.
(note: most screens have some sort of back button, which returns the user to the previous screen in the logical order. Self explanatory, hence not mentioned in descriptions.)


Data files format (text files):
userdata.txt:
Format:
<date> //only once at the top of the file, controls daily limits
<username>,<pin>,<balance>,<dailylimit>,<usedlimit>
<username>,<pin>,<balance>,<dailylimit>,<usedlimit>

Example:
Sun Nov 9 2025
placeholder123,1111,25,1000
username2,1111,100,0


transactiondata.txt:
Format:
<username>,<transactionvalue>,<transactiondate>
<username>,<transactionvalue>,<transactiondate>


Example:
placeholder123,-300,Sun Nov 9 2025 @ 22:58:44
username2,+500,Sun Nov 9 2025 @ 22:59:31


