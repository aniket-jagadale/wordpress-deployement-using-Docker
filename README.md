# WordPress with Docker Compose
![](/img/architecturediagram.png)

This project sets up a WordPress site with a MySQL database using Docker Compose. It's a production-ready configuration suitable for local development, testing, or small-scale deployments.

## Prerequisites

- Docker installed on your system
- Docker Compose installed on your system
- Port 80 available on the host machine

## Project Structure

```
wordpress-docker/
├── docker-compose.yml    # Docker Compose configuration
├── README.md             # This file
└── (optional) .env       # For environment variables (not included)
```

## Quick Start

1. **Clone or create the project directory**
   ```bash
   mkdir wordpress-docker
   cd wordpress-docker
   ```

2. **Create the `docker-compose.yml` file** with the following content:

   ```yaml
   services:
     wordpress:
       image: wordpress:latest
       ports:
         - "80:80"
       environment:
         WORDPRESS_DB_HOST: db
         WORDPRESS_DB_USER: root
         WORDPRESS_DB_PASSWORD: root@123
         WORDPRESS_DB_NAME: wordpressdb
       depends_on:
         - db
       networks:
         - net1

     db:
       image: mysql:latest
       environment:
         MYSQL_ROOT_PASSWORD: root@123
         MYSQL_DATABASE: wordpressdb
       networks:
         - net1
       volumes:
         - val1:/var/lib/mysql

   networks:
     net1:
       driver: bridge

   volumes:
     val1:
   ```

3. **Start the services**
   ```bash
   docker-compose up -d
   ```

4. **Verify the containers are running**
   ```bash
   docker ps
   ```

5. **Access WordPress**
   - Open your browser and navigate to: `http://localhost` or `http://your-server-ip`
   - Follow the WordPress installation wizard

## Commands Reference

| Command | Description |
|---------|-------------|
| `docker-compose up -d` | Start all services in detached mode |
| `docker-compose down` | Stop and remove all containers, networks |
| `docker-compose down -v` | Stop and remove all containers, networks, and volumes |
| `docker-compose ps` | List running containers for this project |
| `docker-compose logs` | View logs from all services |
| `docker-compose logs wordpress` | View logs specifically for WordPress |
| `docker-compose logs db` | View logs specifically for MySQL |
| `docker-compose restart` | Restart all services |
| `docker-compose stop` | Stop all services without removing |
| `docker-compose start` | Start previously stopped services |

## Configuration Details

### Services

1. **WordPress Container**
   - Uses the official WordPress image (latest version)
   - Maps host port 80 to container port 80
   - Connects to MySQL database via environment variables
   - Waits for the database to be ready before starting
    
2. **MySQL Database Container**
   - Uses the official MySQL image
   - Sets root password and creates initial database
   - Data is persisted to a Docker volume

### Networking
- Custom bridge network `net1` isolates the WordPress and database containers
- Containers can communicate using service names (`wordpress`, `db`)

### Persistence
- Database data is stored in Docker volume `val1`
- WordPress uploads and themes are stored within the container (consider adding a volume for `/var/www/html/wp-content` for production)

## Security Notes

⚠️ **Important Security Considerations:**

1. **Change default passwords** before deploying to production
2. **Use strong passwords** for MySQL root user and WordPress admin
3. **Consider using environment variables** instead of hardcoded values
4. **Add SSL/TLS** for production deployments
5. **Implement proper firewall rules** for production servers

For production use, consider:
- Using `.env` files for sensitive data
- Implementing database backups
- Setting up HTTPS with a reverse proxy
- Configuring WordPress security plugins
## Output Images 
![browser page](/img/output.png)

![](/img/powershellui.png)
## Troubleshooting

### Common Issues

1. **Port 80 already in use**
   - Change the port mapping in `docker-compose.yml` to `"8080:80"`

2. **Database connection errors**
   - Ensure MySQL container is running: `docker ps | grep mysql`
   - Check logs: `docker-compose logs db`

3. **Permission issues**
   - WordPress may need write permissions: `docker-compose exec wordpress chown -R www-data:www-data /var/www/html`

4. **Container won't start**
   - Check for syntax errors: `docker-compose config`
   - View detailed logs: `docker-compose logs`

### Viewing Logs
```bash
# All services
docker-compose logs

# Specific service
docker-compose logs wordpress
docker-compose logs db

# Follow logs in real-time
docker-compose logs -f wordpress
```

### Accessing Containers
```bash
# Access WordPress container shell
docker-compose exec wordpress bash

# Access MySQL container shell
docker-compose exec db bash

# Access MySQL database
docker-compose exec db mysql -u root -p
```

## Maintenance

### Backup Database
```bash
docker-compose exec db mysqldump -u root -proot@123 wordpressdb > backup-$(date +%Y%m%d).sql
```

### Update Containers
```bash
# Pull latest images
docker-compose pull

# Recreate containers with new images
docker-compose up -d
```

### Cleanup
```bash
# Remove all containers and networks
docker-compose down

# Remove everything including volumes (WARNING: deletes all data)
docker-compose down -v
```

## WordPress Features Demonstrated

Once WordPress is running, you'll have access to:
- **WordPress 6.9+** with Gutenberg block editor
- **Block patterns** for quick page creation
- **Site editor** for full-site customization
- **Styles customization** for colors, fonts, and layouts

## Support

For issues related to:
- Docker/Docker Compose: Check [Docker documentation](https://docs.docker.com/)
- WordPress: Check [WordPress documentation](https://wordpress.org/support/)
- MySQL: Check [MySQL documentation](https://dev.mysql.com/doc/)

---

**Note**: This setup is intended for development and testing. For production deployments, additional security measures and configuration optimizations are required.