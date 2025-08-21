# Subscription Management Database Setup Guide

This guide will help you set up the SQL database and connect it to the backend server.

## Prerequisites

- MySQL Server installed and running
- Node.js and npm installed
- All dependencies already installed (run `npm install` in Backend directory if needed)

## Database Setup Steps

### 1. Initialize the Database

Run the database initialization script to create the database and tables:

```bash
cd Backend
node init-db.js
```

This script will:
- Create the `subscription_management` database
- Create the `users` and `subscriptions` tables
- Insert sample data for testing

### 2. Configure Environment Variables

The `.env` file has been created with default configurations. Update the values as needed:

```env
# Database Configuration
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_mysql_password
DB_NAME=subscription_management
DB_PORT=3306

# JWT Secret
JWT_SECRET=your_jwt_secret_key

# Email Configuration (for reminders)
EMAIL_USER=your_email@gmail.com
EMAIL_PASS=your_app_password
```

### 3. Database Schema

The database includes the following tables:

#### Users Table
- `id` (INT, Primary Key, Auto Increment)
- `name` (VARCHAR(100), Not Null)
- `email` (VARCHAR(100), Not Null, Unique)
- `password` (VARCHAR(255), Not Null)
- `email_reminders_enabled` (BOOLEAN, Default: TRUE)
- `created_at` (TIMESTAMP, Default: CURRENT_TIMESTAMP)

#### Subscriptions Table
- `id` (INT, Primary Key, Auto Increment)
- `user_id` (INT, Not Null, Foreign Key to users.id)
- `service_name` (VARCHAR(100), Not Null)
- `amount` (DECIMAL(10,2), Not Null)
- `billing_date` (DATE, Not Null)
- `status` (ENUM('Active', 'Expired'), Default: 'Active')
- `created_at` (TIMESTAMP, Default: CURRENT_TIMESTAMP)
- `start_date` (DATE)
- `end_date` (DATE)
- `duration_days` (INT)

### 4. Start the Server

After setting up the database, start the backend server:

```bash
npm start
```

The server will start on port 3000.

## API Endpoints

### Authentication
- `POST /api/register` - Register a new user
- `POST /api/login` - Login user

### User Management
- `GET /api/profile` - Get current user profile (requires authentication)
- `POST /api/settings` - Update user settings (requires authentication)

### Subscriptions
- `GET /api/subscriptions` - Get all subscriptions for current user (requires authentication)
- `POST /api/subscriptions` - Add a new subscription (requires authentication)
- `PUT /api/subscriptions/:id` - Update a subscription (requires authentication)
- `DELETE /api/subscriptions/:id` - Delete a subscription (requires authentication)
- `GET /api/subscriptions/upcoming` - Get subscriptions expiring within 7 days (requires authentication)

### Email Reminders
- `GET /api/trigger-email-reminders` - Manually trigger email reminders (for testing)

## Testing the Setup

1. **Test Database Connection**: The server should log "Connected to MySQL database" on startup
2. **Test Registration**: Use POST `/api/register` with JSON body:
   ```json
   {
     "name": "Test User",
     "email": "test@example.com",
     "password": "password123"
   }
   ```
3. **Test Login**: Use POST `/api/login` with JSON body:
   ```json
   {
     "email": "test@example.com",
     "password": "password123"
   }
   ```

## Troubleshooting

### Common Issues

1. **Database Connection Failed**
   - Ensure MySQL is running
   - Check credentials in `.env` file
   - Verify MySQL user has proper permissions

2. **Access Denied for User**
   - Run: `mysql -u root -p`
   - Execute: `GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' WITH GRANT OPTION;`
   - Execute: `FLUSH PRIVILEGES;`

3. **Database Doesn't Exist**
   - Run the initialization script: `node init-db.js`

4. **Port Already in Use**
   - Change port in server.js or kill the process using port 3000

### Manual Database Creation

If the initialization script fails, you can manually create the database:

```sql
-- Connect to MySQL
mysql -u root -p

-- Create database
CREATE DATABASE subscription_management;
USE subscription_management;

-- Run the schema.sql file
source schema.sql;
```

## Security Notes

- Change the default JWT_SECRET in production
- Use strong MySQL passwords
- Configure proper CORS settings for production
- Use HTTPS in production
- Never commit sensitive credentials to version control
