# Upgrade Guide: hello-genai Python Application

This guide provides comprehensive instructions for upgrading from the original hello-genai Python application to the improved version, including troubleshooting common issues.

## Overview

The improved version includes several enhancements:
- Fixed port configuration
- Enhanced environment variable handling
- Health check endpoint
- Improved logging
- Request validation
- Response caching
- Security headers
- Rate limiting
- API documentation
- Optimized Docker configuration

## Upgrade Steps

### Option 1: Replace the Original Files

1. **Backup your original files:**
   ```bash
   cp -r py-genai py-genai.bak
   ```

2. **Copy the improved files to replace the original:**
   ```bash
   cp -r improved-py-genai/* py-genai/
   ```

3. **Update docker-compose.yml:**
   ```bash
   cp improved-docker-compose.yml docker-compose.yml
   ```

### Option 2: Use Both Versions Side by Side

1. **Keep both versions in the project:**
   - Original: `py-genai/`
   - Improved: `improved-py-genai/`

2. **Update docker-compose.yml to include both services:**
   ```yaml
   services:
     # Original Python service
     python-genai:
       build:
         context: ./py-genai
         dockerfile: Dockerfile
       ports:
         - "8081:8081"
       # ... other configuration ...

     # Improved Python service
     improved-python-genai:
       build:
         context: ./improved-py-genai
         dockerfile: Dockerfile
       ports:
         - "8083:8081"  # Use a different host port
       # ... other configuration ...
   ```

## Required Dependencies

The improved version requires additional Python packages:
- Flask-Caching
- Flask-Limiter
- flask-swagger-ui
- gunicorn

These are already included in the improved `requirements.txt` file.

## Environment Variables

The improved version supports additional environment variables:
- `LOG_LEVEL`: Set the logging level (default: "INFO")

Existing environment variables continue to work as before:
- `LLM_BASE_URL`: The base URL of the LLM API
- `LLM_MODEL_NAME`: The model name to use
- `PORT`: The port to run the application on
- `DEBUG`: Enable debug mode

## New Features

### Health Check Endpoint

The improved version includes a health check endpoint at `/health` that returns:
```json
{
  "status": "healthy",
  "llm_api": "ok",
  "timestamp": "2023-09-15T12:34:56.789Z"
}
```

### API Documentation

The improved version includes Swagger UI documentation at `/api/docs` that provides:
- API endpoint descriptions
- Request/response schemas
- Example requests

### Enhanced User Interface

The improved version includes a completely redesigned user interface with:
- Modern, responsive design that works on all device sizes
- Dark mode support with user preference saving
- Message suggestions for first-time users
- Clear chat history functionality
- Auto-resizing message input
- Animated loading indicators
- Feature cards highlighting key capabilities
- Improved footer with useful links
- Timestamps for messages

### Structured Responses

The improved version now supports structured, formatted responses:
- Markdown formatting for AI responses (headers, lists, code blocks, etc.)
- Enhanced system prompt to instruct the AI to provide structured content
- Example feature to demonstrate formatting capabilities
- Proper styling for formatted elements
- Improved readability for complex information

To see a demonstration of the structured formatting:
1. Run the improved application
2. Click the "Show structured example" button in the suggestions
3. The application will display an example of formatted content

### Security Headers

The improved version adds security headers to all responses:
- X-Content-Type-Options: nosniff
- X-Frame-Options: SAMEORIGIN
- X-XSS-Protection: 1; mode=block
- Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline'

### Rate Limiting

The improved version includes rate limiting:
- 10 requests per minute for the `/api/chat` endpoint
- 200 requests per day overall
- 50 requests per hour overall

## Docker Improvements

The improved Dockerfile:
- Uses multi-stage builds to reduce image size
- Runs as a non-root user for better security
- Includes a health check
- Sets environment variables for better Python container performance

## Testing the Upgrade

1. **Build and run the improved version:**
   ```bash
   docker-compose up --build python-genai
   ```

2. **Access the application:**
   - Web interface: http://localhost:8081/
   - API documentation: http://localhost:8081/api/docs
   - Health check: http://localhost:8081/health
   - Structured example: Click "Show structured example" in the UI

3. **Test the chat functionality:**
   - Send a message through the web interface
   - Or use curl:
     ```bash
     curl -X POST http://localhost:8081/api/chat \
       -H "Content-Type: application/json" \
       -d '{"message":"Hello, how are you?"}'
     ```

4. **Test the structured responses:**
   - Ask questions that would benefit from structured responses:
     - "Explain Docker containers"
     - "Give me a tutorial on Python Flask"
     - "What are the best practices for web security?"
   - The AI will respond with properly formatted, structured content

## Troubleshooting

### Common Issues and Solutions

#### 1. Static Files Not Loading

If CSS, JavaScript, or images are not loading properly:

**Symptoms:**
- UI appears unstyled
- Icons are missing
- JavaScript functionality doesn't work

**Solutions:**
- Verify that the static directory structure is correct:
  ```bash
  ls -la py-genai/static
  ```
- Check that the Flask app is configured to serve static files:
  ```python
  app = Flask(__name__, static_folder='static')
  ```
- Update the Content Security Policy to allow loading resources:
  ```python
  response.headers['Content-Security-Policy'] = "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'"
  ```
- Use the test_static.py script to verify static file serving:
  ```bash
  python test_static.py
  ```
- Consider using inline CSS and JavaScript as a fallback solution

#### 2. API Connection Issues

If the application can't connect to the LLM API:

**Symptoms:**
- Error messages when sending chat messages
- Health check shows LLM API status as "not_configured" or "error"

**Solutions:**
- Verify that the LLM_BASE_URL environment variable is set correctly:
  ```bash
  docker-compose exec python-genai env | grep LLM
  ```
- Check that the LLM API is accessible from the container:
  ```bash
  docker-compose exec python-genai curl -I $LLM_BASE_URL
  ```
- If using host.docker.internal, ensure it's properly configured in docker-compose.yml:
  ```yaml
  extra_hosts:
    - "host.docker.internal:host-gateway"
  ```

#### 3. Port Conflicts

If the application can't bind to the specified port:

**Symptoms:**
- Container fails to start
- Error message about address already in use

**Solutions:**
- Check if another process is using the port:
  ```bash
  lsof -i :8081
  ```
- Change the port mapping in docker-compose.yml:
  ```yaml
  ports:
    - "8082:8081"  # Map to a different host port
  ```

#### 4. Container Permission Issues

If the container has permission problems:

**Symptoms:**
- Permission denied errors in logs
- Container exits unexpectedly

**Solutions:**
- Check the Dockerfile to ensure proper permissions:
  ```dockerfile
  RUN chown -R appuser:appuser /app
  ```
- Verify that the non-root user has access to all necessary directories:
  ```bash
  docker-compose exec python-genai ls -la /app
  ```

### General Troubleshooting Steps

1. **Check the logs:**
   ```bash
   docker-compose logs python-genai
   ```

2. **Verify environment variables:**
   ```bash
   docker-compose exec python-genai env | grep LLM
   ```

3. **Check the health endpoint:**
   ```bash
   curl http://localhost:8081/health
   ```

4. **Run the test script:**
   ```bash
   docker-compose exec python-genai python test_static.py
   ```

5. **Revert to the original version if needed:**
   ```bash
   # If you used Option 1
   cp -r py-genai.bak/* py-genai/
   ```

## Advanced Customization

### Customizing the UI

The improved UI uses CSS variables for easy customization. You can modify these variables in the index.html file:

```css
:root {
    --primary-color: #0078D7;
    --primary-dark: #005a9e;
    --text-color: #333;
    --light-text: #666;
    --border-color: #ddd;
    --border-radius: 8px;
    --box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}
```

### Adding Custom Middleware

You can add custom middleware to the Flask application for additional functionality:

```python
@app.before_request
def custom_middleware():
    # Your custom middleware logic here
    pass
```

### Extending the API

To add new API endpoints, follow this pattern:

```python
@app.route('/api/new-endpoint', methods=['POST'])
@limiter.limit("10 per minute")
def new_endpoint():
    # Your endpoint logic here
    return jsonify({'result': 'success'})
```

### Customizing Docker Configuration

To customize the Docker configuration, modify the Dockerfile:

```dockerfile
# Add custom environment variables
ENV CUSTOM_VAR="value"

# Add custom startup command
CMD ["python", "custom_script.py"]
```

## Conclusion

This upgrade significantly improves the security, reliability, and performance of the hello-genai Python application. The new features like API documentation, health checks, improved error handling, and enhanced UI make the application more robust and user-friendly.

By following this guide, you can successfully upgrade your application and take advantage of all the improvements while avoiding common pitfalls.

## Additional Resources

- [Flask Documentation](https://flask.palletsprojects.com/)
- [Docker Documentation](https://docs.docker.com/)
- [Flask-Caching Documentation](https://flask-caching.readthedocs.io/)
- [Flask-Limiter Documentation](https://flask-limiter.readthedocs.io/)
- [Swagger UI Documentation](https://swagger.io/tools/swagger-ui/)
