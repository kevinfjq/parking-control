# 🚗 Parking Control API

> A comprehensive REST API for managing parking spots in residential complexes, built with Spring Boot and PostgreSQL.

[![Java](https://img.shields.io/badge/java-%23ED8B00.svg?style=for-the-badge&logo=openjdk&logoColor=white)](https://www.java.com/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F?style=for-the-badge&logo=spring-boot&logoColor=white)](https://spring.io/projects/spring-boot)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Maven](https://img.shields.io/badge/Maven-C71A36?style=for-the-badge&logo=apache-maven&logoColor=white)](https://maven.apache.org/)

---

## 🎯 Overview

The Parking Control API is a robust backend service designed to manage parking spots in residential buildings. It provides complete CRUD operations for parking spot management, including validation for license plates, parking spot numbers, and apartment-block combinations.

### Key Benefits
- **Conflict Prevention**: Ensures no duplicate license plates or parking spots
- **Apartment Management**: Links parking spots to specific apartments and blocks
- **Audit Trail**: Tracks registration dates for all parking spots
- **RESTful Design**: Clean, intuitive API endpoints
- **Validation**: Comprehensive input validation and error handling

---

## ✨ Features

### 🔧 Core Features
- **CRUD Operations**: Create, Read, Update, Delete parking spots
- **Conflict Detection**: Prevents duplicate entries for license plates and parking spots
- **Apartment Association**: Links parking spots to specific apartments and blocks
- **Pagination Support**: Efficient data retrieval with pagination
- **Data Validation**: Comprehensive input validation using Bean Validation
- **Cross-Origin Support**: CORS enabled for frontend integration

### 🛡️ Business Rules
- One parking spot per apartment-block combination
- Unique license plate enforcement
- Unique parking spot number enforcement
- Automatic timestamp generation for registrations

---

## 🛠️ Tech Stack

| Technology | Version | Purpose |
|------------|---------|---------|
| **Java** | 17+ | Primary programming language |
| **Spring Boot** | 3.2.2 | Web framework and dependency injection |
| **Spring Data JPA** | 3.2.2 | Object-relational mapping |
| **PostgreSQL** | 13+ | Primary database |
| **Maven** | 3.8+ | Build tool and dependency management |
| **Bean Validation** | 3.0+ | Input validation |
| **Jackson** | 2.15+ | JSON serialization |

---

## 📁 Project Structure

```
parking-control/
├── src/
│   ├── main/
│   │   ├── java/com/api/parkingcontrol/
│   │   │   ├── ParkingControlApplication.java    # Main application class
│   │   │   ├── configs/
│   │   │   │   └── DateConfig.java               # Date/time configuration
│   │   │   ├── controllers/
│   │   │   │   └── ParkingSpotController.java    # REST endpoints
│   │   │   ├── dtos/
│   │   │   │   └── ParkingSpotDto.java           # Data transfer objects
│   │   │   ├── models/
│   │   │   │   └── ParkingSpotModel.java         # JPA entities
│   │   │   ├── repositories/
│   │   │   │   └── ParkingSpotRepository.java    # Data access layer
│   │   │   └── services/
│   │   │       └── ParkingSpotService.java       # Business logic
│   │   └── resources/
│   │       └── application.properties            # Application configuration
│   └── test/
│       └── java/com/api/parkingcontrol/
│           └── ParkingControlApplicationTests.java
├── pom.xml                                       # Maven configuration
└── README.md
```

---

## 🗄️ Database Setup

### PostgreSQL Installation

#### Windows
```bash
# Download and install PostgreSQL from official website
# https://www.postgresql.org/download/windows/

# Or using Chocolatey
choco install postgresql
```

#### macOS
```bash
# Using Homebrew
brew install postgresql
brew services start postgresql
```

#### Ubuntu/Debian
```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

### Database Creation

1. **Connect to PostgreSQL**:
```bash
# Connect as postgres user
sudo -u postgres psql

# Or on Windows
psql -U postgres
```

2. **Create Database**:
```sql
-- Create the database
CREATE DATABASE "parking-control-db";

-- Create a user (optional)
CREATE USER parking_user WITH PASSWORD 'your_password';

-- Grant privileges
GRANT ALL PRIVILEGES ON DATABASE "parking-control-db" TO parking_user;

-- Exit psql
\q
```

3. **Verify Database**:
```bash
# List databases
psql -U postgres -l

# Connect to the database
psql -U postgres -d parking-control-db
```

### Database Schema

The application uses Hibernate DDL auto-generation, but here's the equivalent SQL for the parking spots table:

```sql
-- Create the parking spots table
CREATE TABLE tb_parking_spot (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    parking_spot_number VARCHAR(10) NOT NULL UNIQUE,
    license_plate_car VARCHAR(7) NOT NULL UNIQUE,
    brand_car VARCHAR(70) NOT NULL,
    model_car VARCHAR(70) NOT NULL,
    color_car VARCHAR(70) NOT NULL,
    registration_date TIMESTAMP NOT NULL,
    responsible_name VARCHAR(130) NOT NULL,
    apartment VARCHAR(30) NOT NULL,
    block VARCHAR(30) NOT NULL,
    
    -- Composite unique constraint for apartment + block
    CONSTRAINT uk_apartment_block UNIQUE (apartment, block)
);

-- Create indexes for better performance
CREATE INDEX idx_license_plate ON tb_parking_spot(license_plate_car);
CREATE INDEX idx_parking_spot_number ON tb_parking_spot(parking_spot_number);
CREATE INDEX idx_apartment_block ON tb_parking_spot(apartment, block);
```

---

## 🚀 Installation

### Prerequisites
- Java 17 or higher
- Maven 3.8+
- PostgreSQL 13+
- Git

### Setup Steps

1. **Clone the repository**:
```bash
git clone https://github.com/kevinfjq/parking-control.git
cd parking-control
```

2. **Configure database connection**:
```properties
# Edit src/main/resources/application.properties
spring.datasource.url=jdbc:postgresql://localhost:5432/parking-control-db
spring.datasource.username=postgres
spring.datasource.password=your_password
spring.jpa.hibernate.ddl-auto=update
```

3. **Build the project**:
```bash
mvn clean install
```

4. **Run the application**:
```bash
mvn spring-boot:run
```

5. **Verify installation**:
```bash
curl http://localhost:8080/
# Should return: "Hello World!"
```

---

## 🔗 API Endpoints

### Base URL: `http://localhost:8080`

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Health check |
| POST | `/parking-spot` | Create a new parking spot |
| GET | `/parking-spot` | Get all parking spots (paginated) |
| GET | `/parking-spot/{id}` | Get parking spot by ID |
| PUT | `/parking-spot/{id}` | Update parking spot |
| DELETE | `/parking-spot/{id}` | Delete parking spot |

### Request/Response Examples

#### Create Parking Spot
```http
POST /parking-spot
Content-Type: application/json

{
  "parkingSpotNumber": "A001",
  "licensePlateCar": "ABC1234",
  "brandCar": "Toyota",
  "modelCar": "Corolla",
  "colorCar": "White",
  "responsibleName": "John Doe",
  "apartment": "101",
  "block": "A"
}
```

#### Get All Parking Spots
```http
GET /parking-spot?page=0&size=10&sort=id,asc
```

#### Response Format
```json
{
  "content": [
    {
      "id": "123e4567-e89b-12d3-a456-426614174000",
      "parkingSpotNumber": "A001",
      "licensePlateCar": "ABC1234",
      "brandCar": "Toyota",
      "modelCar": "Corolla",
      "colorCar": "White",
      "registrationDate": "2024-01-15T10:30:00Z",
      "responsibleName": "John Doe",
      "apartment": "101",
      "block": "A"
    }
  ],
  "pageable": {
    "sort": {
      "sorted": true,
      "unsorted": false
    },
    "pageNumber": 0,
    "pageSize": 10
  },
  "totalElements": 1,
  "totalPages": 1
}
```

---

## 📝 Usage Examples

### Using cURL

#### Create a parking spot:
```bash
curl -X POST http://localhost:8080/parking-spot \
  -H "Content-Type: application/json" \
  -d '{
    "parkingSpotNumber": "A001",
    "licensePlateCar": "ABC1234",
    "brandCar": "Toyota",
    "modelCar": "Corolla",
    "colorCar": "White",
    "responsibleName": "John Doe",
    "apartment": "101",
    "block": "A"
  }'
```

#### Get all parking spots:
```bash
curl http://localhost:8080/parking-spot
```

#### Get parking spot by ID:
```bash
curl http://localhost:8080/parking-spot/123e4567-e89b-12d3-a456-426614174000
```

#### Update a parking spot:
```bash
curl -X PUT http://localhost:8080/parking-spot/123e4567-e89b-12d3-a456-426614174000 \
  -H "Content-Type: application/json" \
  -d '{
    "parkingSpotNumber": "A001",
    "licensePlateCar": "XYZ9876",
    "brandCar": "Honda",
    "modelCar": "Civic",
    "colorCar": "Blue",
    "responsibleName": "Jane Smith",
    "apartment": "101",
    "block": "A"
  }'
```

#### Delete a parking spot:
```bash
curl -X DELETE http://localhost:8080/parking-spot/123e4567-e89b-12d3-a456-426614174000
```
