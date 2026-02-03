# Docker Development Environment

A Docker Compose setup for local development with MySQL, phpMyAdmin, Mailpit, and Meilisearch.

## Services

### MySQL Database
- **Image**: mysql:latest
- **Port**: 3306
- **Root Password**: adminadmin
- **User**: admin / adminadmin
- **Data**: Persisted in `./mysql_data`

### phpMyAdmin
- **Image**: phpmyadmin:latest
- **Port**: 8088
- **URL**: http://localhost:8088
- **Upload Limit**: 128MB

### Mailpit
- **Image**: axllent/mailpit:latest
- **Web UI**: http://localhost:8025
- **SMTP Port**: 1025
- **Data**: Persisted in `./mailpit_data`
- Email testing tool for local development

### Meilisearch
- **Image**: getmeili/meilisearch:latest
- **Port**: 7700
- **URL**: http://localhost:7700
- **Master Key**: your-secure-master-key-change-this-in-production
- **Data**: Persisted in `./meilisearch_data`

## Prerequisites

- Docker
- Docker Compose

## Getting Started

### Start All Services

```bash
docker-compose up -d
```

### Stop All Services

```bash
docker-compose down
```

### View Logs

```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f db
docker-compose logs -f mailpit
docker-compose logs -f meilisearch
```

### Restart Services

```bash
docker-compose restart
```

## Access Points

| Service | URL | Credentials |
|---------|-----|-------------|
| phpMyAdmin | http://localhost:8088 | Server: db<br>User: admin<br>Password: adminadmin |
| Mailpit Web UI | http://localhost:8025 | - |
| Meilisearch | http://localhost:7700 | Master Key: your-secure-master-key-change-this-in-production |
| MySQL | localhost:3306 | User: admin<br>Password: adminadmin |
| SMTP | localhost:1025 | - |

## Database Management

### Connect to MySQL CLI

```bash
docker-compose exec db mysql -u admin -padminadmin
```

### Connect as Root

```bash
docker-compose exec db mysql -u root -padminadmin
```

### Backup Database

```bash
docker-compose exec db mysqldump -u admin -padminadmin [database_name] > backup.sql
```

### Restore Database

```bash
docker-compose exec -T db mysql -u admin -padminadmin [database_name] < backup.sql
```

## Data Persistence

All data is persisted in local directories:
- `mysql_data/` - MySQL database files
- `mailpit_data/` - Mailpit email storage
- `meilisearch_data/` - Meilisearch indexes and data

These directories are excluded from git via `.gitignore`.

## Configuration

### Meilisearch Master Key

⚠️ **Important**: Change the Meilisearch master key in production!

Edit `docker-compose.yml` and update:
```yaml
MEILI_MASTER_KEY: "your-secure-master-key"
```

### MySQL Password

To change MySQL credentials, edit the environment variables in `docker-compose.yml`:
```yaml
MYSQL_ROOT_PASSWORD: your_password
MYSQL_PASSWORD: your_password
```

## Troubleshooting

### Port Conflicts

If ports are already in use, modify the port mappings in `docker-compose.yml`:
```yaml
ports:
  - "new_port:container_port"
```

### Reset Everything

To completely reset all data:
```bash
docker-compose down -v
rm -rf mysql_data/ mailpit_data/ meilisearch_data/
docker-compose up -d
```

### View Container Status

```bash
docker-compose ps
```

## Email Testing with Mailpit

Configure your application to use Mailpit for email:
- **SMTP Host**: localhost (or mailpit from within Docker network)
- **SMTP Port**: 1025
- **No authentication required**

View all sent emails at: http://localhost:8025

## Meilisearch Usage

### API Example

```bash
# Health check
curl http://localhost:7700/health

# Create an index
curl -X POST 'http://localhost:7700/indexes' \
  -H 'Authorization: Bearer your-secure-master-key-change-this-in-production' \
  -H 'Content-Type: application/json' \
  --data-binary '{
    "uid": "movies",
    "primaryKey": "id"
  }'
```

## License

This is a development environment setup. Configure appropriately for production use.
