# AuthAction JWT Authentication with JWKS

A Node.js implementation for validating AuthAction JWT tokens using JSON Web Key Sets (JWKS).

## Overview

This project demonstrates how to integrate AuthAction authentication into a Node.js API using JSON Web Key Sets (JWKS) for JWT validation. It includes:

- Express API with public and protected endpoints
- Dynamic JWT validation using JWKS
- Token signature verification, expiration validation, and claims extraction
- OOP-based design with proper error handling
- Caching mechanism for JWKS to improve performance

## Features

- ✅ Public and protected routes
- ✅ Dynamic JWT validation using JWKS (no hardcoded public keys)
- ✅ Proper error handling with detailed messages
- ✅ JWKS caching for performance optimization
- ✅ OOP-based architecture with clean separation of concerns

## Project Structure

```
├── src/
│   ├── config/          # Configuration settings
│   │   └── config.js
│   ├── controllers/     # API route handlers
│   │   └── apiController.js
│   ├── middleware/      # Auth middleware
│   │   └── authMiddleware.js
│   ├── services/        # Business logic (JWKS service)
│   │   └── jwksService.js
│   ├── utils/           # Error handling utilities
│   │   └── errorHandler.js
│   └── app.js           # Application entry point
├── .env                 # Environment variables
├── package.json
└── README.md
```

## Installation

1. Clone the repository:
```bash
git clone https://github.com/yourusername/authaction-jwt-node.git
cd authaction-jwt-node
```

2. Install dependencies:
```bash
npm install
```

3. Create a `.env` file in the root directory:
```
PORT=3000
AUTH_DOMAIN=https://your-authaction-tenant-domain
API_IDENTIFIER=your-authaction-api-identifier
NODE_ENV=development
```

4. Start the server:
```bash
npm start
```

For development with auto-reload:
```bash
npm run dev
```

## Getting an AuthAction Token

To obtain a JWT token using the Client Credentials Flow:

```bash
curl --request POST \
  --url https://your-authaction-tenant-domain/oauth2/m2m/token \
  --header 'content-type: application/json' \
  --data '{
    "client_id": "your-authaction-m2m-app-clientid",
    "client_secret": "your-authaction-m2m-app-client-secret",
    "audience": "your-authaction-api-identifier",
    "grant_type": "client_credentials"
  }'
```

This will return a response containing an `access_token`.

## API Endpoints

### Public Endpoint

No authentication required:

```bash
curl --request GET \
  --url http://localhost:3000/public
```

Expected response:
```json
{
  "message": "This is a public message!"
}
```

### Protected Endpoint

Requires valid JWT token:

```bash
curl --request GET \
  --url http://localhost:3000/protected \
  --header 'Authorization: Bearer YOUR_ACCESS_TOKEN'
```

Expected response:
```json
{
  "message": "This is a protected message!",
  "user": {
    "sub": "user_identifier",
    "email": "user@example.com"
  }
}
```

## JWKS Integration Explanation

### How JWKS Works

JSON Web Key Set (JWKS) is a set of keys containing the public keys used to verify any JSON Web Token (JWT) issued by the authorization server. When a client sends a request with a JWT, the server:

1. Extracts the Key ID (`kid`) from the JWT header
2. Fetches the corresponding public key from the JWKS endpoint
3. Uses this key to verify the JWT's signature

This dynamic approach allows for key rotation without service disruption.

### Implementation Details

The `JwksService` class handles the JWKS integration:

1. **Key Retrieval**: The service connects to AuthAction's JWKS endpoint (`/.well-known/jwks.json`) to fetch public keys.

2. **Caching**: To improve performance, keys are cached using `node-cache`.

3. **Token Verification**: The service verifies:
   - Token signature (using the correct public key)
   - Token expiration
   - Required claims (sub, aud)

4. **Error Handling**: The service provides detailed error messages for various failure scenarios.

## Common Issues and Solutions

### Audience Validation

If you encounter an audience validation error:

```
JWT verification error: JsonWebTokenError: jwt audience invalid
```

Ensure your `API_IDENTIFIER` in the `.env` file matches the `aud` claim in your token.

### JWKS Endpoint Access

Verify your `AUTH_DOMAIN` is correctly set. The JWKS endpoint should be accessible at:
```
https://your-authaction-tenant-domain/.well-known/jwks.json
```

### Token Format

Ensure you're passing the token correctly in the Authorization header:
```
Authorization: Bearer eyJhbGciOiJSUzI1NiIs...
```

## Security Considerations

- Always use HTTPS in production
- Implement rate limiting to prevent abuse
- Consider token refresh strategies for long-lived sessions
- Monitor JWKS endpoint availability

## Dependencies

- `express`: Web framework
- `jsonwebtoken`: JWT validation library
- `jwks-rsa`: JWKS client
- `axios`: HTTP client
- `node-cache`: Caching library
- `dotenv`: Environment variable management

## Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License

[MIT](https://choosealicense.com/licenses/mit/)
