# Google Drive Clone — AWS EC2 Project

## 1. Project Overview

This project is a Google Drive clone application developed and deployed on AWS EC2.

The application allows users to:

- Upload files
- List uploaded files
- Delete uploaded files
- Store actual files in Amazon S3
- Store file metadata in SQLite
- Access the application through a public EC2 IP address
- Run the application inside a Docker container

The project demonstrates the integration of a web application with AWS cloud services, database storage, Docker containerization, and EC2 deployment.

---

## 2. Project Objectives

The main objectives of this project are:

1. Build a simple file management web application.
2. Implement file upload functionality.
3. Store uploaded files in Amazon S3.
4. Store file metadata in a SQLite database.
5. Implement file listing functionality.
6. Implement file deletion functionality.
7. Containerize the application using Docker.
8. Deploy the Dockerized application on AWS EC2.
9. Make the application publicly accessible through the EC2 public IP.
10. Maintain the source code using Git and GitHub.

---

## 3. Technology Stack

### Frontend

- HTML
- CSS
- JavaScript
- Fetch API

### Backend

- Node.js
- Express.js
- Multer
- SQLite
- AWS SDK for JavaScript

### Cloud & Deployment

- Amazon EC2
- Amazon S3
- AWS IAM
- Docker
- GitHub

---

## 4. Project Structure

The project is organized into frontend and backend components.

```text
google-drive-clone/
│
├── backend/
│   ├── database.js
│   ├── s3.js
│   ├── server.js
│   ├── package.json
│   ├── package-lock.json
│   └── .env
│
├── frontend/
│   ├── index.html
│   ├── style.css
│   └── script.js
│
├── Dockerfile
├── .dockerignore
└── .gitignore
```

---

## 5. System Architecture

The application follows the architecture shown below:

```text
                         Internet
                            |
                            v
                    Public EC2 IP : 80
                            |
                            v
                  Docker Container : 3000
                            |
                            v
                    Node.js + Express
                       /          \
                      /            \
                     v              v
                 SQLite          Amazon S3
                Database        File Storage
                   |
                   v
             File Metadata
```

### Architecture Explanation

1. The user accesses the application through a web browser.
2. The request reaches the public IP address of the AWS EC2 instance.
3. Port 80 on EC2 forwards traffic to port 3000 of the Docker container.
4. The Node.js and Express.js backend handles API requests.
5. SQLite stores file metadata such as file name and S3 key.
6. Amazon S3 stores the actual uploaded files.
7. The EC2 IAM role provides the application with permission to access S3.

---

## 6. Frontend

The frontend provides a simple interface for managing files.

The main features of the frontend are:

- File selection
- File upload
- Displaying uploaded files
- Refreshing the file list
- Deleting files
- Displaying success and error messages

### Frontend Files

#### `index.html`

Provides the structure of the web application.

It contains:

- Application header
- Upload form
- File input
- Upload button
- File listing section
- Refresh button
- Delete buttons

#### `style.css`

Provides the visual styling of the application, including:

- Page layout
- Buttons
- File cards
- Upload section
- Responsive design
- Colors and spacing

#### `script.js`

Handles communication between the frontend and backend using the Fetch API.

It performs:

- GET requests to retrieve files
- POST requests to upload files
- DELETE requests to remove files
- Updating the UI after operations

---

## 7. Backend

The backend is developed using Node.js and Express.js.

The backend is responsible for:

- Handling HTTP requests
- Receiving uploaded files
- Uploading files to Amazon S3
- Storing metadata in SQLite
- Retrieving file metadata
- Deleting files from S3
- Removing deleted file metadata from SQLite

---

## 8. API Endpoints

The application provides the following API endpoints.

### 8.1 Get All Files

```text
GET /api/files
```

This endpoint retrieves all files stored in the SQLite database.

The files are returned in descending order based on the upload time.

---

### 8.2 Upload a File

```text
POST /api/files
```

This endpoint accepts a file using multipart form data.

The upload process is:

1. User selects a file.
2. Frontend sends the file to the backend.
3. Multer processes the uploaded file.
4. A unique S3 key is generated.
5. The file is uploaded to Amazon S3.
6. File metadata is stored in SQLite.
7. A success response is returned to the frontend.

Example S3 key:

```text
uploads/1757000000000-example.txt
```

---

### 8.3 Delete a File

```text
DELETE /api/files/:id
```

This endpoint deletes a file using its database ID.

The deletion process is:

1. Backend finds the file metadata using the ID.
2. The S3 key is retrieved from SQLite.
3. The actual file is deleted from Amazon S3.
4. The corresponding metadata is deleted from SQLite.
5. A success response is returned.

---

## 9. SQLite Database

SQLite is used to store the metadata of uploaded files.

The database contains a table named:

```text
files
```

### Database Schema

| Column | Type | Description |
|---|---|---|
| id | INTEGER | Unique ID of the file |
| file_name | TEXT | Original name of the uploaded file |
| s3_key | TEXT | Location/key of the file in S3 |
| uploaded_at | DATETIME | Date and time of upload |

### Important Design

The actual file is **not stored inside SQLite**.

SQLite only stores metadata.

For example:

```text
SQLite
    |
    +-- file_name: example.txt
    +-- s3_key: uploads/1757000000000-example.txt
    +-- uploaded_at: timestamp
```

The actual file is stored in Amazon S3.

---

## 10. Amazon S3

Amazon S3 is used as the actual file storage system.

The project uses an S3 bucket for storing uploaded files.

Uploaded files are stored under the:

```text
uploads/
```

prefix.

### Upload Flow

```text
User
 |
 v
Frontend
 |
 v
Express Backend
 |
 v
Amazon S3
 |
 v
File Stored
```

After successfully uploading the file to S3, the S3 key is saved in the SQLite database.

### Delete Flow

```text
User clicks Delete
        |
        v
Express Backend
        |
        v
Find S3 Key from SQLite
        |
        v
Delete File from S3
        |
        v
Delete Metadata from SQLite
```

This ensures that both the actual file and its database record are removed.

---

## 11. AWS IAM Role

An IAM role was attached to the EC2 instance to allow the application to communicate with Amazon S3.

The EC2 instance uses the IAM role instead of storing AWS access keys inside the application.

This is a more secure approach because:

- AWS credentials do not need to be hardcoded.
- AWS access keys do not need to be placed inside the Docker image.
- The application can obtain temporary credentials through the EC2 instance role.
- Credentials are not committed to GitHub.

The EC2 instance was configured with the IAM role:

```text
GoogleDriveEC2Role
```

The role provides permissions required for S3 operations.

---

## 12. AWS EC2 Configuration

The application was deployed on an AWS EC2 instance.

### EC2 Details

- Region: `ap-south-1`
- Region Name: Mumbai
- Instance Type: `t3.micro`
- Operating System: Ubuntu
- Application Port: `3000`
- Public HTTP Port: `80`

The EC2 security group was configured to allow:

- SSH traffic on port `22`
- HTTP traffic on port `80`

Port 80 allows users to access the application through a web browser.

---

## 13. EC2 Server Setup

The EC2 instance was configured with the required tools.

The following software was installed and configured:

- AWS CLI
- Git
- Docker
- Node.js dependencies through Docker

The project repository was cloned onto the EC2 instance.

Example:

```bash
git clone <repository>
```

The application was then built into a Docker image.

---

## 14. Docker Containerization

The application was containerized using Docker.

A `Dockerfile` was created in the root directory of the project.

The Docker image contains:

- Node.js
- Backend source code
- Frontend source code
- Application dependencies
- SQLite dependencies

The application runs on port `3000` inside the Docker container.

The EC2 host exposes the application through port `80`.

### Port Mapping

```text
EC2 Port 80
     |
     v
Docker Port 3000
     |
     v
Express Application
```

The Docker container was started using:

```bash
docker run -d --name google-drive-clone -p 80:3000 google-drive-clone
```

---

## 15. Docker Configuration

The project contains a `.dockerignore` file.

It prevents unnecessary or incompatible files from being copied into the Docker image.

Important ignored files include:

```text
node_modules
backend/node_modules
.env
drive.db
.git
```

Ignoring the local `node_modules` directory was important because native SQLite dependencies needed to be compiled inside the Docker environment.

---

## 16. Git and GitHub

Git was used for version control.

The project source code was committed and pushed to GitHub.

Repository:

```text
ShailendraS80/google-drive-clone
```

The repository contains:

- Frontend code
- Backend code
- Dockerfile
- `.dockerignore`
- `.gitignore`
- Package configuration
- Project source files

Sensitive files such as AWS credentials, `.env` files, private keys, and local database files were excluded from Git using `.gitignore`.

---

## 17. File Upload Workflow

The complete upload workflow is:

```text
1. User selects a file
          |
          v
2. Frontend sends POST request
          |
          v
3. Express receives the file
          |
          v
4. Multer processes the file
          |
          v
5. Unique S3 key is generated
          |
          v
6. File uploaded to Amazon S3
          |
          v
7. Metadata inserted into SQLite
          |
          v
8. Success response sent to frontend
          |
          v
9. File appears in the file list
```

---

## 18. File Listing Workflow

The listing workflow is:

```text
User opens application
        |
        v
Frontend sends GET /api/files
        |
        v
Express queries SQLite
        |
        v
SQLite returns file metadata
        |
        v
Backend sends JSON response
        |
        v
Frontend displays files
```

The file list displays:

- File name
- Upload date and time
- Delete button

---

## 19. File Deletion Workflow

The deletion workflow is:

```text
User clicks Delete
        |
        v
Frontend sends DELETE request
        |
        v
Backend finds file in SQLite
        |
        v
Backend gets S3 key
        |
        v
File deleted from S3
        |
        v
Metadata deleted from SQLite
        |
        v
Frontend refreshes file list
```

This ensures that the S3 object and its SQLite metadata remain synchronized.

---

## 20. Testing Performed

The following functionality was tested during development and deployment.

### Upload Testing

Files were uploaded successfully through:

- Local application
- Docker container
- Public EC2 application

The uploaded files were verified in Amazon S3.

### List Testing

The application successfully retrieved uploaded file metadata from SQLite and displayed the files on the frontend.

### Delete Testing

Files were deleted through the frontend.

After deletion:

- The file disappeared from the application.
- The corresponding S3 object was removed.
- The corresponding SQLite record was removed.

### Docker Testing

The Docker image was successfully built and the application ran inside a Docker container.

### EC2 Testing

The Docker container was successfully deployed on EC2.

The application was accessed using the EC2 public IP address.

---

## 21. Problems Encountered and Solutions

### 21.1 SQLite Docker Dependency Error

During the initial Docker build/run process, a SQLite native module compatibility issue occurred.

The problem was caused by the local `node_modules` directory being copied into the Docker image.

The local compiled SQLite dependency was not compatible with the Docker environment.

#### Solution

The following directories were added to `.dockerignore`:

```text
node_modules
backend/node_modules
```

The Dockerfile was also configured to install the dependencies inside the Docker environment.

After rebuilding the image, the SQLite issue was resolved.

---

### 21.2 Frontend API Connection Error on EC2

Initially, the frontend used:

```text
http://localhost:3000/api/files
```

This worked when testing locally.

However, when the application was opened through the EC2 public IP, the browser interpreted `localhost` as the user's own computer rather than the EC2 server.

#### Solution

The frontend API URL was changed to:

```text
/api/files
```

This makes the frontend communicate with the backend through the same host from which the application was loaded.

After rebuilding and redeploying the Docker container, the public application worked correctly.

---

### 21.3 AWS Credentials for Local Docker Testing

During local Docker testing, the container needed permission to access Amazon S3.

For local testing, AWS credentials were made available to the container through the local AWS configuration.

Credentials were not placed inside the Dockerfile or committed to GitHub.

For the EC2 deployment, the EC2 IAM role was used instead.

---

## 22. Security Considerations

The following security practices were followed:

- AWS credentials were not hardcoded into source code.
- `.env` files were excluded from Git.
- Private SSH keys were not added to the repository.
- AWS credentials were not included in the Docker image.
- EC2 IAM role was used for S3 access.
- `.gitignore` was used to prevent sensitive files from being committed.

---

## 23. Final Application Flow

The final application works as follows:

```text
                         USER
                          |
                          v
                    Web Browser
                          |
                          v
                AWS EC2 Public IP
                       Port 80
                          |
                          v
                  Docker Container
                       Port 3000
                          |
                          v
                  Node.js + Express
                     /          \
                    /            \
                   v              v
              SQLite DB       Amazon S3
             File Metadata   Actual Files
```

---

## 24. Final Features

The completed application provides:

- File upload
- File listing
- File deletion
- Amazon S3 storage
- SQLite metadata storage
- REST API
- Docker containerization
- AWS EC2 deployment
- IAM-based S3 access
- Public web access
- GitHub source code management

---

## 25. Project Verification

The final deployment was verified by performing the following operations:

```text
Application Access
       |
       v
Upload File
       |
       v
File appears in application
       |
       v
File exists in Amazon S3
       |
       v
Metadata exists in SQLite
       |
       v
Delete File
       |
       v
File removed from application
       |
       v
File removed from Amazon S3
       |
       v
Metadata removed from SQLite
```

All major project requirements were successfully implemented and tested.

---

## 26. Conclusion

The Google Drive Clone project successfully demonstrates a complete cloud-based file management application.

The project combines a web frontend with a Node.js and Express.js backend. SQLite is used to store file metadata, while Amazon S3 is used to store the actual files.

The application was containerized using Docker and deployed on an AWS EC2 instance. An EC2 IAM role was used to provide secure access to Amazon S3 without storing AWS credentials in the application.

The final application supports file upload, file listing, and file deletion and is publicly accessible through the EC2 instance.

This project demonstrates practical experience with:

- Web development
- REST APIs
- Node.js
- Express.js
- SQLite
- Amazon S3
- AWS IAM
- AWS EC2
- Docker
- Git
- GitHub
- Cloud deployment

---

## 27. Project Status

```text
Frontend                  : Completed
Backend                   : Completed
SQLite Database           : Completed
Amazon S3 Integration     : Completed
IAM Role Configuration    : Completed
Dockerization             : Completed
EC2 Deployment            : Completed
Public Access             : Completed
CRUD Testing              : Completed
GitHub Repository         : Completed
Documentation             : Completed
Screenshots               : To be added
```

---

## 28. Repository

GitHub Repository:

```text
ShailendraS80/google-drive-clone
```

Project documentation for the internship submission is maintained separately inside the `foundation/EC2` directory.
