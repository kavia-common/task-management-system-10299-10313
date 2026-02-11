# Todo Database Schema

## Database Information
- **Database Name**: myapp
- **Database Type**: PostgreSQL
- **Port**: 5000
- **User**: appuser

## Connection String
```
postgresql://appuser:dbuser123@localhost:5000/myapp
```

## Tables

### users
Stores user account information for authentication.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | SERIAL | PRIMARY KEY | Unique user identifier |
| email | VARCHAR(255) | UNIQUE NOT NULL | User's email address (used for login) |
| password_hash | VARCHAR(255) | NOT NULL | Bcrypt hashed password |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Account creation timestamp |
| updated_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Last update timestamp |

**Indexes:**
- Primary key on `id`
- Unique constraint on `email`

### todos
Stores todo items associated with users.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | SERIAL | PRIMARY KEY | Unique todo identifier |
| user_id | INTEGER | NOT NULL, FOREIGN KEY REFERENCES users(id) ON DELETE CASCADE | Owner of the todo |
| title | VARCHAR(255) | NOT NULL | Todo title/summary |
| description | TEXT | NULL | Detailed description of the todo |
| completed | BOOLEAN | DEFAULT FALSE | Completion status |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Todo creation timestamp |
| updated_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Last update timestamp |

**Indexes:**
- Primary key on `id`
- Index on `user_id` for faster queries (idx_todos_user_id)

**Foreign Keys:**
- `user_id` references `users(id)` with CASCADE delete

## Seed Data

### Test Users
Two test users are included for development:

1. **demo@example.com** (user_id: 1)
   - Password: `password123`
   - Has 4 todo items (3 incomplete, 1 complete)

2. **user@example.com** (user_id: 2)
   - Password: `password123`
   - Has 1 todo item

### Sample Todos
5 sample todo items are included demonstrating:
- Incomplete tasks
- Completed tasks
- Tasks with descriptions
- Tasks for different users

## SQL Commands Used

```sql
-- Create users table
CREATE TABLE IF NOT EXISTS users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create todos table
CREATE TABLE IF NOT EXISTS todos (
    id SERIAL PRIMARY KEY,
    user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    completed BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create index for performance
CREATE INDEX IF NOT EXISTS idx_todos_user_id ON todos(user_id);
```

## Notes
- Password hash uses bcrypt with cost factor 12
- All timestamps are in UTC
- Deleting a user will cascade delete all their todos
- The database uses the public schema by default
