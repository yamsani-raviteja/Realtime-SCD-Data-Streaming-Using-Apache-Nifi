# Real-Time Streaming Data with Apache NiFi, Snowflake, and Docker on AWS EC2

This README provides a comprehensive guide to setting up a real-time data streaming pipeline using Apache NiFi and Snowflake, deployed with Docker on AWS EC2 instances.

## Prerequisites

1. **AWS Account**: Ensure you have an active AWS account.
2. **Docker**: Install Docker on your local machine.
3. **AWS CLI**: Install and configure AWS CLI.
4. **SSH Key Pair**: Create an SSH key pair to access your EC2 instances.
5. **Snowflake Account**: Ensure you have access to a Snowflake account.

## Architecture Overview

1. **Apache NiFi**: Used for data ingestion and processing.
2. **Snowflake**: Used as the data warehouse for storing and querying the processed data.
3. **Docker**: Containers for deploying Apache NiFi and other necessary services.
4. **AWS EC2**: Instances for running Docker containers.

## Steps to Set Up

### 1. Launch EC2 Instances

1. **Login to AWS Management Console**.
2. **Navigate to EC2 Dashboard**.
3. **Launch a new EC2 instance**:
   - Choose Amazon Linux 2 AMI.
   - Select instance type (t2.medium or higher recommended).
   - Configure instance details (number of instances, VPC settings, etc.).
   - Add storage (minimum 20 GB recommended).
   - Add tags (optional).
   - Configure security group to allow inbound SSH (port 22) and any other required ports (e.g., 2080 for NiFi).
   - Review and launch.

4. **Connect to EC2 instance** using SSH:
   ```sh
   ssh -i /path/to/your-key.pem ec2-user@your-ec2-public-dns
   ```

### 2. Install Docker on EC2 Instance

1. **Update the package index**:
   ```sh
   sudo yum update -y
   ```

2. **Install Docker**:
   ```sh
   sudo amazon-linux-extras install docker -y
   ```

3. **Start Docker service**:
   ```sh
   sudo service docker start
   ```

4. **Add the `ec2-user` to the `docker` group**:
   ```sh
   sudo usermod -aG docker ec2-user
   ```

5. **Log out and log back in to apply the group change**.

### 3. Deploy Apache NiFi using Docker

1. **Pull the Apache NiFi Docker image**:
   ```sh
   docker pull apache/nifi
   ```

2. **Run the NiFi container**:
   ```sh
   docker run -d -p 2080:2080 --name nifi apache/nifi
   ```

3. **Access NiFi Web UI**:
   - Open a web browser and navigate to `http://your-ec2-public-dns:2080/nifi`.

### 4. Configure Apache NiFi for Data Ingestion

1. **Drag and drop processors** in the NiFi UI to create a data flow.
2. **Configure processors** for:
   - Data source (e.g., Kafka, HTTP, File).
   - Data transformation (e.g., JoltTransformJSON).
   - Data destination (Snowflake).

### 5. Connect Apache NiFi to Snowflake

1. **Create Snowflake credentials** and store them securely.
2. **Install Snowflake JDBC Driver** in the NiFi container:
   - Download the driver.
   - Copy it into the NiFi container:
     ```sh
     docker cp /path/to/snowflake-jdbc-driver.jar nifi:/opt/nifi/nifi-current/lib/
     ```
3. **Configure NiFi Snowflake Processor**:
   - Add a processor for Snowflake connection.
   - Configure the connection details using your Snowflake credentials.

### 6. Set Up Docker Compose (Optional)

1. **Create a `docker-compose.yml` file** to define services: Use the given file and write all the requirements in the yaml format. 

2. **Deploy using Docker Compose**:
   ```sh
   docker-compose up -d
   ```

### 7. Using snowflake for SCD data

1. **Monitor the SCD data flow** through the merge conditions.
2. **Scale the SCD Data** as needed based on the data available and need to updated.

## Cleanup

1. **Terminate EC2 instances** when they are no longer needed.
2. **Remove Docker containers and volumes**:
   ```sh
   docker-compose down -v
   ```

## Troubleshooting

- **NiFi UI not accessible**: Check security group settings and ensure port 2080 is open.
- **Snowflake connection issues**: Verify credentials and network configurations.

## Conclusion

You have now set up a real-time streaming data pipeline using Apache NiFi and Snowflake, running on Docker containers within AWS EC2 instances. This setup allows for scalable and flexible data processing and storage solutions.

For further information, refer to the official documentation of Apache NiFi, Snowflake, Docker, and AWS EC2.
