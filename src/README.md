# Mergington High School Activities API

A super simple FastAPI application that allows students to view and sign up for extracurricular activities.

## Features

- View all available extracurricular activities
- Teachers can sign up and unregister students after logging in
- Students can view activities and their participants without logging in

## Getting Started

1. Install the dependencies:

   ```
   pip install fastapi uvicorn
   ```

2. Run the application:

   ```
   python app.py
   ```

3. Open your browser and go to:
   - API documentation: http://localhost:8000/docs
   - Alternative documentation: http://localhost:8000/redoc

## API Endpoints

| Method | Endpoint                                                          | Description                                                         |
| ------ | ----------------------------------------------------------------- | ------------------------------------------------------------------- |
| GET    | `/activities`                                                     | Get all activities with their details and current participant count |
| POST   | `/auth/login`                                                      | Log in as a teacher; accepts a JSON `username` and `password`        |
| GET    | `/auth/me`                                                         | Get the current teacher session                                     |
| POST   | `/auth/logout`                                                     | End the current teacher session                                     |
| POST   | `/activities/{activity_name}/signup?email=student@mergington.edu` | Sign up a student (teacher login required)                           |
| DELETE | `/activities/{activity_name}/unregister?email=student@mergington.edu` | Unregister a student (teacher login required)                    |

## Teacher accounts

Teacher credentials are configured in `teachers.json`. Passwords must be stored
as salted PBKDF2-SHA256 hashes, not plaintext. To generate a hash, run this from
the `src` directory and enter the assigned password at the prompt:

```sh
python -c "import getpass; from app import hash_password; print(hash_password(getpass.getpass()))"
```

Add the username and generated hash to the `teachers` object in `teachers.json`:

```json
{
  "teachers": {
    "teacher1": {
      "password_hash": "pbkdf2_sha256$600000$..."
    }
  }
}
```

The login cookie is HTTP-only and expires after eight hours. Teacher credentials
are loaded at startup; sessions are held in memory and cleared when the server
restarts.

## Data Model

The application uses a simple data model with meaningful identifiers:

1. **Activities** - Uses activity name as identifier:

   - Description
   - Schedule
   - Maximum number of participants allowed
   - List of student emails who are signed up

2. **Students** - Uses email as identifier:
   - Name
   - Grade level

All data is stored in memory, which means data will be reset when the server restarts.
