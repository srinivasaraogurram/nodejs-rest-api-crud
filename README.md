# nodejs-rest-api-crud

To update the CRUD API implementation using AWS Lambda, we'll use AWS API Gateway to expose the endpoints and AWS Lambda functions to handle the logic. We'll also use AWS DynamoDB as our database to store the books.

### Step 1: Set Up AWS Services

1. **Create a DynamoDB Table:**
   - Table name: `Books`
   - Primary key: `id` (Number)

2. **Create Lambda Functions:**
   - Create separate Lambda functions for each CRUD operation (Create, Read, Update, Delete).

### Step 2: Implement Lambda Functions

#### Create a Book (POST)

**Lambda Function Code:**
```javascript
const AWS = require('aws-sdk');
const dynamo = new AWS.DynamoDB.DocumentClient();

exports.handler = async (event) => {
    const { title, author, published_year } = JSON.parse(event.body);
    const id = Date.now(); // Simple ID generation

    const newBook = { id, title, author, published_year };

    await dynamo.put({
        TableName: 'Books',
        Item: newBook
    }).promise();

    return {
        statusCode: 201,
        body: JSON.stringify(newBook),
    };
};
```

#### Read All Books (GET)

**Lambda Function Code:**
```javascript
const AWS = require('aws-sdk');
const dynamo = new AWS.DynamoDB.DocumentClient();

exports.handler = async () => {
    const result = await dynamo.scan({ TableName: 'Books' }).promise();

    return {
        statusCode: 200,
        body: JSON.stringify(result.Items),
    };
};
```

#### Update a Book (PUT)

**Lambda Function Code:**
```javascript
const AWS = require('aws-sdk');
const dynamo = new AWS.DynamoDB.DocumentClient();

exports.handler = async (event) => {
    const { id } = event.pathParameters;
    const { title, author, published_year } = JSON.parse(event.body);

    const updatedBook = { id: parseInt(id), title, author, published_year };

    await dynamo.put({
        TableName: 'Books',
        Item: updatedBook
    }).promise();

    return {
        statusCode: 200,
        body: JSON.stringify(updatedBook),
    };
};
```

#### Delete a Book (DELETE)

**Lambda Function Code:**
```javascript
const AWS = require('aws-sdk');
const dynamo = new AWS.DynamoDB.DocumentClient();

exports.handler = async (event) => {
    const { id } = event.pathParameters;

    await dynamo.delete({
        TableName: 'Books',
        Key: { id: parseInt(id) }
    }).promise();

    return {
        statusCode: 204,
        body: null,
    };
};
```

### Step 3: Set Up API Gateway

1. **Create a new API:**
   - Create a new REST API in API Gateway.

2. **Define Resources and Methods:**
   - **POST /books**: Integrate with the Create Book Lambda function.
   - **GET /books**: Integrate with the Read All Books Lambda function.
   - **PUT /books/{id}**: Integrate with the Update Book Lambda function.
   - **DELETE /books/{id}**: Integrate with the Delete Book Lambda function.

3. **Deploy the API:**
   - Deploy the API to a stage (e.g., `dev`).

### Step 4: Test the API

You can use cURL or Postman to test the API endpoints as described earlier, but now using the API Gateway URL.

#### Example cURL Command for Creating a Book:
```sh
curl -X POST https://your-api-id.execute-api.region.amazonaws.com/dev/books \
     -H "Content-Type: application/json" \
     -d '{"title": "The Great Gatsby", "author": "F. Scott Fitzgerald", "published_year": 1925}'
```

This setup will give you a serverless CRUD API using AWS Lambda, API Gateway, and DynamoDB. Let me know if you need any further assistance!
