# Data-Consealment

Create Data concealment

Data concealment
1.	Employees Table:

CREATE TABLE Employees (
                  EmployeeID INT PRIMARY KEY,
                  Username VARCHAR(50) NOT NULL,
                  Password VARCHAR(50) NOT NULL,
                  Designation VARCHAR(50) NOT NULL
                       );

2.	Files Table:
CREATE TABLE Files (
                 FileID INT PRIMARY KEY,
                 FileName VARCHAR(255) NOT NULL,
                 FilePath VARCHAR(255) NOT NULL
                   );

3.	Fileaccess Table:

CREATE TABLE FileAccess (
                  EmployeeID INT,
                  FileID INT,
                  PRIMARY KEY (EmployeeID, FileID),
                  FOREIGN KEY (EmployeeID) REFERENCES Employees(EmployeeID),
                  FOREIGN KEY (FileID) REFERENCES Files(FileID)
                       );


DATA INSERTION:::


INSERT INTO Employees (EmployeeID, Username, Password, Designation) VALUES
(1, 'john_doe', 'password123', 'Manager'),
(2, 'jane_smith', 'pass456', 'Employee'),
(3, 'robert_jones', 'secure789', 'Employee'),
(4, 'lisa_white', 'access321', 'Employee'),
(5, 'david_brown', 'key987', 'Manager'),
(6, 'percy_jackson', 'percy123', 'Analyst');


INSERT INTO Files (FileID, FileName, FilePath) VALUES
(101, 'ProjectReport.pdf', '/files/project_report.pdf'),
(102, 'MeetingMinutes.doc', '/files/meeting_minutes.doc'),
(103, 'FinancialStatements.xlsx', '/files/financial_statements.xlsx'),
(104, 'TrainingVideo.mp4', '/files/training_video.mp4'),
(105, 'EmployeeHandbook.docx', '/files/employee_handbook.docx'),
(106, 'Placement.ppt', '/files/placement.ppt'),
(107, 'Economies.docx', '/files/economies.docx');


INSERT INTO FileAccess (EmployeeID, FileID) VALUES
(1, 101),  
(2, 101),  
(1, 102),  
(3, 102),  
(3, 103),  
(2, 104),  
(4, 104), 
(5, 104), 
(5, 105),  
(5,106),
(4, 105);


QUERIES::::


1) List files and the number of employees with access, ordered by access count:

SELECT Files.FileID, Files.FileName, Files.FilePath, COUNT(FileAccess.EmployeeID) AS AccessCount
FROM Files
LEFT JOIN FileAccess ON Files.FileID = FileAccess.FileID
GROUP BY Files.FileID, Files.FileName, Files.FilePath
ORDER BY AccessCount DESC, Files.FileID ASC;

2) Retrieve employees who do not have access to any files:

SELECT Employees.EmployeeID, Employees.Username
FROM Employees
WHERE NOT EXISTS (
    SELECT 1 FROM FileAccess
    WHERE FileAccess.EmployeeID = Employees.EmployeeID
);

3) List the files with access details, showing usernames of employees with access:

SELECT Files.*, GROUP_CONCAT(Employees.Username) AS UsernamesWithAccess
FROM Files
LEFT JOIN FileAccess ON Files.FileID = FileAccess.FileID
LEFT JOIN Employees ON FileAccess.EmployeeID = Employees.EmployeeID
GROUP BY Files.FileID, Files.FileName, Files.FilePath;

4) Retrieve files with no access, potentially for cleanup or security auditing:

SELECT Files.*
FROM Files
LEFT JOIN FileAccess ON Files.FileID = FileAccess.FileID
WHERE FileAccess.FileID IS NULL;

5) Count the number of managers with access to each file:

SELECT Files.FileID, Files.FileName, COUNT(Employees.EmployeeID) AS ManagerCount
FROM Files
JOIN FileAccess ON Files.FileID = FileAccess.FileID
JOIN Employees ON FileAccess.EmployeeID = Employees.EmployeeID
WHERE Employees.Designation = 'Manager'
GROUP BY Files.FileID, Files.FileName;

6) Find files that only managers have access to:

SELECT Files.FileID, Files.FileName, COUNT(FileAccess.EmployeeID) AS ManagerCount
FROM Files
JOIN FileAccess ON Files.FileID = FileAccess.FileID
JOIN Employees ON FileAccess.EmployeeID = Employees.EmployeeID
WHERE Employees.Designation = 'Manager'
GROUP BY Files.FileID, Files.FileName
HAVING ManagerCount > 0
AND ManagerCount = (SELECT COUNT(*) FROM Employees WHERE Designation = 'Manager');

7) Identify employees with the same access as a specific employee (EmployeeID = 2 in this case):

SELECT DISTINCT E1.EmployeeID, E1.Username
FROM FileAccess AS F1
JOIN FileAccess AS F2 ON F1.FileID = F2.FileID AND F2.EmployeeID = 2
JOIN Employees AS E1 ON F1.EmployeeID = E1.EmployeeID
WHERE F1.EmployeeID <> 2;

8) Retrieve employees who have access to the same files as a specific manager (Manager with EmployeeID = 1):

SELECT DISTINCT E.EmployeeID, E.Username
FROM FileAccess AS F1
JOIN FileAccess AS F2 ON F1.FileID = F2.FileID AND F2.EmployeeID = 1
JOIN Employees AS E ON F1.EmployeeID = E.EmployeeID
WHERE F1.EmployeeID <> 1;

9)List employees and the total number of files they have access to, ordered by access count:

SELECT Employees.EmployeeID, Employees.Username, COUNT(FileAccess.FileID) AS AccessCount
FROM Employees
LEFT JOIN FileAccess ON Employees.EmployeeID = FileAccess.EmployeeID
GROUP BY Employees.EmployeeID, Employees.Username
ORDER BY AccessCount DESC, Employees.EmployeeID ASC;

10)Find files with access details, including the number of managers and non-managers with access:

SELECT Files.FileID, Files.FileName, 
       COUNT(CASE WHEN Employees.Designation = 'Manager' THEN 1 END) AS ManagerCount,
       COUNT(CASE WHEN Employees.Designation != 'Manager' THEN 1 END) AS NonManagerCount
FROM Files
LEFT JOIN FileAccess ON Files.FileID = FileAccess.FileID
LEFT JOIN Employees ON FileAccess.EmployeeID = Employees.EmployeeID
GROUP BY Files.FileID, Files.FileName;

0 comments on commit 5a460ae
@adithyasai28
Comment
 
Leave a comment
 
Footer
© 2024 GitHub, Inc.
Footer navigation
Terms
Privacy
Security
Status
Docs
Contact
Manage cookies
Do not share my personal information
Copied!
