# Serverless Framework AWS Tutorial Notes

Technologies used:
- Serverless Framework
- Nodejs
- AWS
  - API Gateway
  - Lambda Functions
  - DynamoDB
  - Simple Mail Service
  - Simple Queue Service
  - S3 

## Overview of Architecture 

Sevices:
- Auction Service
- Auth Service
- Notification Service

### Auction Service

6 Lambda Functions in total.

5 Lambda requests come from API Gateway, public gate that triggers endpoints like traditional Rest API. All of which get authorized before getting executed:
- createAuction: creates an auction
- getAuctions: gets auctions based on status with queryParams
- getAuction: gets auctin by the auction id
- placeBid: users place a bid on the auction 
- updateAuctionPicture: updated to S3 for storage

Final Lambda: ProcessAuction is triggered by AWS 
- checks whether the time is up with the auction 
- closes the auction 
- emails the seller and bidder if they are successful or not

### Auth Service

Handles uses Auth0 to handle authentication and authorization, authorizes Lambda functions with JSON web token 

Overview
- user gets authenticated to Auth0 
- Auth0 sends 200 ok with JSON web token
- sends request to API Gateway with Authorization header that has token 
- API gateway calls authorizer if successful then API Gateway will forward request to Lambda Function with JWT containing user details

```
functions:
  createAuction:
    handler: src/handlers/createAuction.handler
    events:
      - http:
          method: POST
          path: /auction
          cors: true
          authorizer: ***{Auth Service ARN here}*** <----
```

### Notification Service

processAuction will sends to Simple Queue Service and this in turn calls a sendMail Lambda which passes email to Simple Email Service

### High Level View of Services

![image](https://user-images.githubusercontent.com/10774349/229365271-c0552e9c-cc62-43aa-a466-c54f1432cc55.png)


