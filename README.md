# Dynamic Web Application Deployment on AWS

This project demonstrates the deployment of a dynamic PHP web application on AWS using a highly available, scalable, and secure architecture. The setup includes VPC networking, RDS database, Auto Scaling, and SSL security.

## Architecture Overview

The application uses a multi-tier architecture:
- Presentation Layer: Auto-scaled EC2 instances behind an Application Load Balancer
- Application Layer: PHP application with Apache web server
- Data Layer: Amazon RDS MySQL database
- Storage Layer: S3 bucket for static assets
- Network Layer: Custom VPC with public and private subnets

## Prerequisites

- AWS Account with appropriate permissions
- Domain name for SSL/HTTPS setup
- Basic understanding of Linux, PHP, and MySQL
- AWS CLI installed and configured

## Infrastructure Components

### Networking
- Custom VPC with DNS hostnames enabled
- Public and private subnets across availability zones
- Internet Gateway for public access
- NAT Gateway for private subnet internet access
- Route tables for subnet traffic management
- EC2 Instance Connect endpoints

### Compute
- EC2 instances with Auto Scaling
- Launch template with custom AMI
- Application Load Balancer
- Target groups for load balancing

### Database
- Amazon RDS MySQL instance
- Flyway for database migrations
- Secure subnet placement

### Storage
- S3 bucket for application files
- IAM roles for S3 access

### Security
- SSL/TLS certificate from AWS Certificate Manager
- Security groups
- Route 53 for DNS management

## Deployment Steps

### 1. Network Setup
```bash
# Create VPC and enable DNS hostnames
# Create Internet Gateway and attach to VPC
# Create public and private subnets
# Configure auto-assign IP for public subnets
# Create NAT Gateway
# Configure route tables
```

### 2. Storage Setup
```bash
# Create S3 bucket
aws s3 mb s3://your-bucket-name
# Upload application files
aws s3 sync ./your-app-files s3://your-bucket-name/
```

### 3. Database Setup
```bash
# Create RDS instance
# Configure security groups
# Run Flyway migrations
```

### 4. Application Deployment
```bash
#!/bin/bash
# Update system
sudo yum update -y

# Install Apache and PHP
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

# Install PHP and extensions
sudo dnf install -y \
    php php-pdo php-openssl php-mbstring \
    php-exif php-fileinfo php-xml php-ctype \
    php-json php-tokenizer php-curl php-cli \
    php-fpm php-mysqlnd php-bcmath php-gd \
    php-cgi php-gettext php-intl php-zip

# Install MySQL client
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
sudo dnf install -y mysql-community-server

# Configure Apache
sudo sed -i '/<Directory "\/var\/www\/html">/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/httpd/conf/httpd.conf

# Deploy application
aws s3 sync s3://your-bucket-name /var/www/html
```

### 5. Load Balancer Setup
1. Create target group
2. Create Application Load Balancer
3. Configure security groups
4. Add instances to target group

### 6. Auto Scaling Setup
1. Create AMI from configured instance
2. Create launch template
3. Create Auto Scaling group
4. Configure scaling policies

### 7. DNS and SSL Setup
1. Register domain in Route 53
2. Create A record pointing to ALB
3. Request SSL certificate
4. Configure HTTPS listener

## Database Migration

```bash
#!/bin/bash
# Flyway migration script
flyway -url=jdbc:mysql://your-rds-endpoint:3306/dbname \
  -user=username \
  -password=password \
  -locations=filesystem:sql \
  migrate
```

## Security Considerations

- All application instances in private subnets
- Database accessible only from application subnet
- SSL/TLS encryption for data in transit
- Security groups restrict access to necessary ports
- IAM roles for EC2 to S3 access

## Monitoring and Maintenance

- CloudWatch for metrics and logs
- Auto Scaling for handling load variations
- Regular database backups via RDS
- SSL certificate renewal monitoring

## Cost Optimization

- Use Auto Scaling to match capacity with demand
- Consider Reserved Instances for predictable workloads
- Monitor and optimize RDS instance size
- Use S3 lifecycle policies for cost-effective storage

## Troubleshooting

1. Check EC2 instance logs in /var/log/httpd/
2. Monitor RDS performance metrics
3. Verify security group configurations
4. Check Auto Scaling group activity
5. Validate SSL certificate state
