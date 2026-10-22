# Zabbix Report Center

**English** | [简体中文](README_zh.md)

---

An enterprise-grade Zabbix monitoring report center featuring automated metric collection, Excel report generation, and scheduled email dispatch.

## Features

- **Multi-Instance Management**: Seamlessly manage and switch between multiple Zabbix server instances.
- **Flexible Authentication**: Supports both modern API Token authentication (Zabbix 5.4+) and classic Username/Password.
- **Automated Report Generation**: Periodically collects monitoring metrics and compiles them into professional Excel spreadsheets.
- **Email Delivery**: Automatically sends generated reports to designated recipients via SMTP.
- **Cron Scheduling**: Built-in task scheduling powered by standard Cron expressions.
- **Real-Time Inspection**: Live querying and on-demand inspection of hosts, items, and triggers.
- **Access Control & RBAC**: Role-based user permissions (Administrator and Standard User roles).
- **Audit Logging**: Comprehensive operation audit logging for security and compliance.
- **Credential Encryption**: Sensitive credentials (passwords, tokens) are securely encrypted at rest.

## UI Preview

### Administrator Dashboard

![Admin Dashboard](doc/images/admin-dashboard.png)

Administrators have full access to all system modules, including system management, user management, and configuration.

### Standard User Dashboard

![User Dashboard](doc/images/user-dashboard.png)

Standard users have access to essential operational modules such as monitoring information, scheduled tasks, and alert reviews.

## Tech Stack

### Backend
- **FastAPI** + **Uvicorn**: High-performance asynchronous API framework and ASGI web server.
- **Databases**: SQLite (default), MySQL, or PostgreSQL.
- **APScheduler**: Advanced Python task scheduler.
- **Pandas** + **OpenPyXL**: High-performance data manipulation and Excel report engine.

### Frontend
- **Vue 3** + **Composition API**: Progressive frontend framework.
- **TypeScript**: Static type safety across the application.
- **Element Plus**: Comprehensive enterprise UI component library.
- **Pinia**: Centralized state management.
- **Vue Router**: Client-side routing.
- **Axios**: HTTP client for API communication.
- **Vite**: Next-generation frontend build tooling.

## Quick Start

### Local Deployment

**Prerequisites:** Python 3.8+, Node.js 20.19+

#### Backend Setup

```bash
# 1. Clone the repository
git clone https://github.com/gurululo/zabbix-report-center.git
cd zabbix-report-center

# 2. Install Python dependencies
pip install -r requirements.txt

# 3. Configure environment variables
cp .env.example .env

# Generate encryption keys (Linux / macOS)
python -c "import secrets; print('SECRET_KEY=' + secrets.token_urlsafe(32))" >> .env
python -c "import secrets; print('ENCRYPTION_KEY=' + secrets.token_urlsafe(32))" >> .env

# Or using Windows PowerShell
python -c "import secrets; print('SECRET_KEY=' + secrets.token_urlsafe(32))" | Out-File -Append .env -Encoding utf8
python -c "import secrets; print('ENCRYPTION_KEY=' + secrets.token_urlsafe(32))" | Out-File -Append .env -Encoding utf8

# 4. Start the backend service
python run.py
```

#### Frontend Setup

```bash
# 1. Navigate to the frontend directory
cd frontend

# 2. Install dependencies
npm install

# 3. Start the development server
npm run dev
```

- **Backend API**: `http://localhost:38204`
- **Frontend Dev Server**: `http://localhost:37201`

Open `http://localhost:37201` in your browser. The first registered account automatically receives Administrator privileges.

### Production Build

```bash
# Build the frontend assets
cd frontend
npm run build

# The compiled static assets will be in the frontend/dist directory
# They can be served via Nginx or hosted directly by the backend service
```

### Docker Deployment

**Prerequisites:** Docker + Docker Compose

#### Option 1: Using Docker Hub Image (Recommended)

```bash
# 1. Create a project directory
mkdir zabbix-report-center && cd zabbix-report-center

# 2. Download configuration templates
curl -O https://raw.githubusercontent.com/gurululo/zabbix-report-center/main/docker-compose.yml.example
curl -O https://raw.githubusercontent.com/gurululo/zabbix-report-center/main/.env.example

# 3. Rename configuration files
mv docker-compose.yml.example docker-compose.yml
mv .env.example .env

# 4. Edit docker-compose.yml and replace "build: ." with:
# image: yoke68/zabbix-report-center:latest
# Adjust port mappings and passwords as needed

# 5. Start the service
docker-compose up -d
```

#### Option 2: Build Locally

```bash
# 1. Clone the repository
git clone https://github.com/gurululo/zabbix-report-center.git
cd zabbix-report-center

# 2. Copy and configure environment files
cp docker-compose.yml.example docker-compose.yml
cp .env.example .env
# Edit .env according to your requirements

# 3. Build and launch
docker-compose up -d --build

# View logs
docker-compose logs -f app

# Stop services
docker-compose down

# Restart services
docker-compose restart
```

Access `http://localhost:37201` to start using the system.

## Configuration

### Environment Variables

Configure key parameters in `.env`:

```env
# JWT signing key
SECRET_KEY=your-random-secret-key

# Symmetric encryption key for Zabbix / SMTP credentials
ENCRYPTION_KEY=your-random-encryption-key

# Database connection URL (optional, defaults to SQLite)
# DATABASE_URL=mysql+pymysql://user:password@localhost/zabbix_report_center

# Debug mode
DEBUG=false
```

Generate a secure random key:
```bash
python -c "import secrets; print(secrets.token_urlsafe(32))"
```

### Zabbix Configuration

Navigate to **System Management > Zabbix Configuration** to register your Zabbix server. Both API Token (Zabbix 5.4+) and Username/Password authentication methods are supported.

### Email Configuration

Navigate to **System Management > Email Configuration** to set up SMTP server details for report deliveries.

## Usage

### Creating Scheduled Tasks

1. Navigate to the **Scheduled Tasks** page.
2. Click **Add Task**.
3. Fill in target metrics, frequency, and email recipient details, then save.

**Common Cron Expression Examples:**
```text
0 9 * * *      # Every day at 09:00
0 */6 * * *    # Every 6 hours
0 9 * * 1      # Every Monday at 09:00
0 9 1 * *      # 1st day of every month at 09:00
```

### Real-Time Metric Queries

Under the **Monitoring Information** section, search and inspect hosts, items, and triggers in real time, and export the current query results to Excel on demand.

## Project Structure

```text
zabbix-report-center/
├── app/                # Backend application
│   ├── api/           # API routes & endpoints
│   ├── models/        # Database models (SQLAlchemy)
│   ├── schemas/       # Request & response validation (Pydantic)
│   ├── services/      # Business logic & services
│   └── utils/         # Helper functions & encryption
├── frontend/           # Vue 3 frontend application
│   ├── src/
│   │   ├── api/       # API client wrappers
│   │   ├── components/# Reusable UI components
│   │   ├── composables/# Vue composables
│   │   ├── router/    # Route configuration
│   │   ├── stores/    # Pinia state stores
│   │   ├── types/     # TypeScript interfaces & types
│   │   ├── utils/     # Frontend utilities
│   │   └── views/     # Page views
│   └── dist/          # Frontend production build output
├── doc/                # Detailed documentation
│   └── images/        # UI screenshots
├── exports/            # Generated export files
├── .env.example        # Environment variable template
├── Dockerfile          # Dockerfile for all-in-one container
├── docker-compose.yml.example  # Docker Compose template
├── requirements.txt    # Python dependencies
└── run.py             # Backend launcher script
```

## Database Support

| Database | Description |
| :--- | :--- |
| **SQLite** | Default, zero-configuration file database (ideal for trial/small deployments). |
| **MySQL** | Recommended for production environments. |
| **PostgreSQL** | Recommended for high-concurrency environments. |

To switch databases, simply set the `DATABASE_URL` environment variable; database tables and schemas will be created automatically on startup.

## Security

- **Password Storage**: Passwords are securely hashed with `Bcrypt`.
- **Sensitive Data at Rest**: Zabbix and SMTP passwords are encrypted using symmetric `Fernet` encryption.
- **Authentication**: Stateless `JWT` tokens with a 24-hour expiration window.
- **Configuration Hygiene**: All critical secrets are decoupled and managed via `.env`.

For further details, refer to [doc/SECURITY.md](doc/SECURITY.md).

## Documentation

- [API Reference](doc/API.md)
- [Deployment Guide](doc/DEPLOYMENT.md)
- [Development Guide](doc/DEVELOPMENT.md)
- [Security Overview](doc/SECURITY.md)

## Troubleshooting & FAQ

**Cannot connect to Zabbix Server**
- Check the Zabbix API URL and verify network connectivity between the server and Zabbix.
- Verify that the API Token or account credentials have appropriate read permissions in Zabbix.

**Email delivery fails**
- Check your SMTP server host, port, and authentication credentials.
- Verify whether your SMTP provider requires SSL/TLS.

**Scheduled task does not run**
- Confirm that the task switch is enabled in the dashboard.
- Double-check the syntax of the configured Cron expression.

**SQLite database is locked**
- Avoid high-frequency concurrent write tasks on SQLite.
- Migrate to MySQL or PostgreSQL for production multi-worker deployments.

## License

[MIT License](LICENSE)
