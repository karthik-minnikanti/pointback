# PointBack

**PointBack** is a lightweight and efficient tool that allows users to navigate through the history of database documents in MongoDB or PostgreSQL. It enables time travel for your data by rolling back to specific points in time, ensuring easy restoration and tracking of changes.

**Status:** Work in Progress

## Key Features

- **Time Travel:** Retrieve any document or row to a specific state at a given timestamp.
- **Rollback Support:** Easily revert changes to any previous version of a document.
- **Database Compatibility:** Supports MongoDB and PostgreSQL.
- **Version Management:** Track and manage changes to documents over time.
- **Stream Integration:** Seamlessly integrates with Kafka using Confluent and MongoDB source connectors.

## Installation

PointBack relies on Confluent, MongoDB source connectors, and Kafka. Follow the steps below to set up the environment and install PointBack.

### Step 1: Set Up Kafka and Confluent

1. Download and install [Confluent Platform](https://www.confluent.io/get-started/).
2. Start Kafka and Confluent services:
   ```bash
   confluent local services start
   ```

### Step 2: Configure MongoDB Source Connector

1. Configure the MongoDB source connector in Confluent by creating a configuration file `mongodb-source-config.json`:
   ```json
   {
     "name": "mongodb-source-connector",
     "config": {
       "connector.class": "com.mongodb.kafka.connect.MongoSourceConnector",
       "connection.uri": "mongodb://localhost:27017",
       "database": "your-database-name",
       "collection": "your-collection-name",
       "topics": "your-topic-name"
     }
   }
   ```
2. Start the MongoDB source connector:
   ```bash
   confluent local services connect start
   confluent local load mongodb-source-connector -- -d mongodb-source-config.json
   ```

### Step 3: Install PointBack

Install PointBack as a Kafka consumer to manage document versions and rollbacks:

#### Example (Node.js):
```bash
npm install pointback
```

#### Example (Python):
```bash
pip install pointback
```

## Configuration

1. Configure PointBack to connect to Kafka and MongoDB.

### Example Configuration (JSON):
```json
{
  "kafka": {
    "bootstrapServers": "localhost:9092",
    "groupId": "pointback-consumer-group",
    "topic": "your-topic-name"
  },
  "database": {
    "type": "mongodb",
    "host": "localhost",
    "port": 27017,
    "username": "your-username",
    "password": "your-password",
    "databaseName": "your-database-name"
  }
}
```

## Usage

### Initialization

Import and initialize the PointBack library in your project:

#### Example (Node.js):
```javascript
const PointBack = require('pointback');

const db = new PointBack({
  kafka: {
    bootstrapServers: 'localhost:9092',
    groupId: 'pointback-consumer-group',
    topic: 'your-topic-name'
  },
  database: {
    type: 'mongodb',
    host: 'localhost',
    port: 27017,
    databaseName: 'myDatabase'
  }
});
```

#### Example (Python):
```python
from pointback import PointBack

pointback = PointBack(
    kafka={
        "bootstrapServers": "localhost:9092",
        "groupId": "pointback-consumer-group",
        "topic": "your-topic-name"
    },
    database={
        "type": "mongodb",
        "host": "localhost",
        "port": 27017,
        "database_name": "my_database"
    }
)
```

### Retrieve a Document by Time

#### Node.js:
```javascript
const document = await db.getDocumentByTime('collectionName', 'documentId', '2025-01-01T10:00:00Z');
console.log(document);
```

#### Python:
```python
document = pointback.get_document_by_time("table_name", "document_id", "2025-01-01T10:00:00Z")
print(document)
```

### Rollback to a Specific Version

#### Node.js:
```javascript
await db.rollbackDocument('collectionName', 'documentId', '2025-01-01T10:00:00Z');
console.log('Rollback successful');
```

#### Python:
```python
pointback.rollback_document("table_name", "document_id", "2025-01-01T10:00:00Z")
print("Rollback successful")
```
