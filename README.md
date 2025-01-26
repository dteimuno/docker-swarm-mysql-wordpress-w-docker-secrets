```markdown
# Docker Stack Deployment for WordPress and MySQL

This project sets up a scalable WordPress application with a MySQL database using Docker Stack and Docker Swarm. The services are configured to leverage secrets for secure handling of sensitive information and utilize overlay networks for communication.

## Features

- **WordPress Service**:
  - Runs the latest WordPress image.
  - Configured to use Docker secrets for database credentials.
  - Exposes the application on port 80.
  - Scalable with `replicas` set to 3.
  - Communicates with the MySQL database over the `backend` network.
  - Accessible through the `frontend` network.

- **MySQL Service**:
  - Runs the latest MySQL image.
  - Configured to use Docker secrets for secure database setup.
  - Stores data persistently using a local volume (`mysqlvolume`).
  - Deploys a single replica of the database service.
  - Ensures the WordPress service is started before database initialization (`depends_on`).

## Architecture

The deployment consists of:

- Two services:
  1. `wordpress`: A load-balanced WordPress service running three replicas.
  2. `wordpress-mysql`: A single MySQL database service with persistent storage.
- Two overlay networks for separation of frontend and backend traffic.
- Secure handling of sensitive data using Docker secrets.

## Prerequisites

1. A Docker Swarm cluster initialized and running.
2. The following Docker secrets must be created externally before deploying the stack:
   - `wordpressdbhost`: The hostname for the WordPress database.
   - `wordpressdbuser`: The database username for WordPress.
   - `wordpressdbpassword`: The database password for WordPress.
   - `wordpressdbname`: The name of the WordPress database.
   - `mysqlrootpassword`: The root password for MySQL.
   - `mysqldatabase`: The name of the database to create in MySQL.
   - `mysqluser`: The database username for MySQL.
   - `mysqlpassword`: The password for the MySQL user.

   Example for creating a Docker secret:
   ```bash
   echo "your-secret-value" | docker secret create wordpressdbpassword -
   ```

3. Ensure that the `mysqlvolume` is created locally to persist database data.

## Usage

1. Deploy the stack using the following command:
   ```bash
   docker stack deploy -c docker-compose.yml wordpress-stack
   ```

2. Verify that the services are running:
   ```bash
   docker service ls
   ```

3. Access the WordPress application at `http://<SWARM_NODE_IP>`.

## Networks

- **Frontend Network**: Used to expose the WordPress service to external traffic.
- **Backend Network**: Used for secure communication between WordPress and the MySQL database.

## Volumes

- **mysqlvolume**: Ensures that MySQL data is stored persistently.

## Notes

- Adjust the number of replicas for the `wordpress` service in the YAML file to meet your scaling needs.
- Secrets must be securely managed and never hardcoded in the YAML file.
- Ensure proper DNS or load balancer configuration for production environments.

## Cleanup

To remove the stack, use:
```bash
docker stack rm wordpress-stack
```
This will stop and remove all services and networks associated with the stack, but the secrets and volume will remain.
```
