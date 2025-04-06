# Improved Python GenAI Application

This is an improved version of the Python GenAI application with enhanced features, security, and performance optimizations.

## Key Improvements

1. **Fixed Port Configuration**: Standardized port configuration across all files (8081)
2. **Enhanced Environment Variable Handling**: Added validation and proper defaults
3. **Health Check Endpoint**: Added `/health` endpoint for container orchestration
4. **Improved Logging**: Configured structured logging with customizable log levels
5. **Request Validation**: Added input validation and sanitization
6. **Response Caching**: Implemented caching to improve performance
7. **Security Headers**: Added security headers to protect against common web vulnerabilities
8. **Rate Limiting**: Added rate limiting to prevent API abuse
9. **API Documentation**: Added Swagger UI for API documentation at `/api/docs`
10. **Optimized Docker Configuration**: Multi-stage build and non-root user
11. **Improved User Interface**: Modern, responsive design with enhanced user experience
12. **Static File Serving**: Properly configured static file serving for CSS, JavaScript, and images

## Environment Variables

The application uses the following environment variables:

- `LLM_BASE_URL`: The base URL of the LLM API
- `LLM_MODEL_NAME`: The model name to use
- `PORT`: The port to run the application on (default: 8081)
- `DEBUG`: Set to "true" to enable debug mode (default: "false")
- `LOG_LEVEL`: Set the logging level (default: "INFO")

## API Endpoints

- `GET /`: Web interface for the chat application
- `POST /api/chat`: Send a message to the AI and get a response
- `GET /health`: Health check endpoint
- `GET /api/docs`: API documentation

## Running the Application

### Using Docker Compose

```bash
docker-compose up python-genai
```

### Running Locally

```bash
cd improved-py-genai
pip install -r requirements.txt
python app.py
```

## Security Considerations

- The application uses security headers to protect against common web vulnerabilities
- Input validation and sanitization is implemented to prevent injection attacks
- Rate limiting is implemented to prevent abuse
- The Docker container runs as a non-root user

## Performance Optimizations

- Response caching is implemented to improve performance
- Multi-stage Docker build reduces image size
- Environment variable validation prevents unnecessary API calls
