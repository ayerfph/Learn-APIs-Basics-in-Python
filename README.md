# Learn APIs Basics in Python

A comprehensive guide to understanding and working with APIs using Python. This repository serves as a complete learning resource for beginners who want to master API fundamentals and practical implementation.

## Table of Contents

1. [What are APIs?](#what-are-apis)
2. [Types of APIs](#types-of-apis)
3. [HTTP Fundamentals](#http-fundamentals)
4. [Python Libraries for APIs](#python-libraries-for-apis)
5. [Making Your First API Request](#making-your-first-api-request)
6. [Working with JSON Data](#working-with-json-data)
7. [HTTP Methods in Detail](#http-methods-in-detail)
8. [Status Codes and Error Handling](#status-codes-and-error-handling)
9. [Authentication Methods](#authentication-methods)
10. [Query Parameters and Headers](#query-parameters-and-headers)
11. [Real-World Examples](#real-world-examples)
12. [Best Practices](#best-practices)
13. [Testing APIs](#testing-apis)
14. [Resources for Further Learning](#resources-for-further-learning)

## What are APIs?

**API** stands for **Application Programming Interface**. Think of it as a waiter in a restaurant:

- **You (Client)** want to order food
- **Kitchen (Server)** prepares the food
- **Waiter (API)** takes your order to the kitchen and brings back your food

APIs allow different software applications to communicate with each other. They define the methods and data formats that applications can use to request and exchange information.

### Why are APIs Important?

- **Data Access**: Get real-time data from services (weather, stock prices, social media)
- **Integration**: Connect different applications and services
- **Automation**: Automate tasks and workflows
- **Scalability**: Build applications that leverage existing services

## Types of APIs

### 1. REST APIs (Most Common)
- Uses standard HTTP methods (GET, POST, PUT, DELETE)
- Stateless communication
- JSON data format
- Easy to understand and implement

### 2. GraphQL APIs
- Single endpoint for all data operations
- Client specifies exactly what data to fetch
- More efficient for complex data requirements

### 3. SOAP APIs
- XML-based protocol
- More rigid structure
- Common in enterprise environments

### 4. WebSocket APIs
- Real-time, bidirectional communication
- Great for live chat, gaming, stock tickers

## HTTP Fundamentals

Before working with APIs, it's essential to understand HTTP (HyperText Transfer Protocol):

### HTTP Request Structure
```
METHOD /path HTTP/1.1
Host: api.example.com
Headers: value
[Body]
```

### HTTP Response Structure
```
HTTP/1.1 200 OK
Headers: value
[Body]
```

## Python Libraries for APIs

### 1. `requests` - The Most Popular Choice

```python
import requests

# Simple and intuitive syntax
response = requests.get('https://api.example.com/data')
```

**Why use requests?**
- Simple and elegant syntax
- Automatic JSON decoding
- Built-in session management
- Excellent documentation

### 2. `urllib` - Built-in Python Library

```python
import urllib.request
import json

# More verbose but no external dependencies
with urllib.request.urlopen('https://api.example.com/data') as response:
    data = json.loads(response.read())
```

### 3. `httpx` - Modern Alternative

```python
import httpx

# Async support and modern features
async with httpx.AsyncClient() as client:
    response = await client.get('https://api.example.com/data')
```

## Making Your First API Request

Let's start with a simple example using a free API:

```python
import requests

# Make a GET request to a public API
response = requests.get('https://jsonplaceholder.typicode.com/posts/1')

# Check if the request was successful
if response.status_code == 200:
    # Parse the JSON response
    data = response.json()
    print(f"Title: {data['title']}")
    print(f"Body: {data['body']}")
else:
    print(f"Error: {response.status_code}")
```

### Step-by-Step Breakdown:

1. **Import the library**: `import requests`
2. **Make the request**: `requests.get(url)`
3. **Check the status**: `response.status_code`
4. **Parse the data**: `response.json()`

## Working with JSON Data

JSON (JavaScript Object Notation) is the most common data format for APIs:

```python
import requests
import json

# Get data from API
response = requests.get('https://jsonplaceholder.typicode.com/users')
users = response.json()

# Working with JSON data
for user in users:
    print(f"Name: {user['name']}")
    print(f"Email: {user['email']}")
    print(f"City: {user['address']['city']}")
    print("-" * 30)

# Pretty print JSON
print(json.dumps(users[0], indent=2))
```

### JSON Tips:
- Use `response.json()` to automatically parse JSON
- Use `json.dumps()` for pretty printing
- Handle nested objects with bracket notation
- Always check if keys exist to avoid KeyError

## HTTP Methods in Detail

### GET - Retrieve Data
```python
import requests

# Get all posts
response = requests.get('https://jsonplaceholder.typicode.com/posts')

# Get specific post
response = requests.get('https://jsonplaceholder.typicode.com/posts/1')
```

### POST - Create New Data
```python
import requests

new_post = {
    'title': 'My New Post',
    'body': 'This is the content of my post',
    'userId': 1
}

response = requests.post(
    'https://jsonplaceholder.typicode.com/posts',
    json=new_post
)

if response.status_code == 201:  # Created
    created_post = response.json()
    print(f"Created post with ID: {created_post['id']}")
```

### PUT - Update Existing Data
```python
import requests

updated_post = {
    'id': 1,
    'title': 'Updated Title',
    'body': 'Updated content',
    'userId': 1
}

response = requests.put(
    'https://jsonplaceholder.typicode.com/posts/1',
    json=updated_post
)
```

### DELETE - Remove Data
```python
import requests

response = requests.delete('https://jsonplaceholder.typicode.com/posts/1')

if response.status_code == 200:
    print("Post deleted successfully")
```

## Status Codes and Error Handling

### Common HTTP Status Codes:

- **200 OK**: Request successful
- **201 Created**: Resource created successfully
- **400 Bad Request**: Invalid request
- **401 Unauthorized**: Authentication required
- **403 Forbidden**: Access denied
- **404 Not Found**: Resource not found
- **500 Internal Server Error**: Server error

### Proper Error Handling:

```python
import requests
from requests.exceptions import RequestException, Timeout, ConnectionError

def safe_api_request(url, timeout=5):
    try:
        response = requests.get(url, timeout=timeout)
        response.raise_for_status()  # Raises an exception for bad status codes
        return response.json()
    
    except Timeout:
        print("Request timed out")
    except ConnectionError:
        print("Connection error occurred")
    except requests.exceptions.HTTPError as e:
        print(f"HTTP error occurred: {e}")
    except RequestException as e:
        print(f"An error occurred: {e}")
    except ValueError:
        print("Invalid JSON response")
    
    return None

# Usage
data = safe_api_request('https://jsonplaceholder.typicode.com/posts/1')
if data:
    print(data)
```

## Authentication Methods

### 1. API Keys
```python
import requests

api_key = "your-api-key-here"

# Method 1: Query parameter
response = requests.get('https://api.example.com/data', params={'api_key': api_key})

# Method 2: Header
headers = {'X-API-Key': api_key}
response = requests.get('https://api.example.com/data', headers=headers)
```

### 2. Bearer Token Authentication
```python
import requests

token = "your-bearer-token"
headers = {'Authorization': f'Bearer {token}'}

response = requests.get('https://api.example.com/data', headers=headers)
```

### 3. Basic Authentication
```python
import requests
from requests.auth import HTTPBasicAuth

# Method 1: Using auth parameter
response = requests.get(
    'https://api.example.com/data',
    auth=HTTPBasicAuth('username', 'password')
)

# Method 2: Using auth tuple
response = requests.get(
    'https://api.example.com/data',
    auth=('username', 'password')
)
```

## Query Parameters and Headers

### Query Parameters
```python
import requests

# Method 1: Using params parameter (recommended)
params = {
    'page': 1,
    'limit': 10,
    'category': 'technology'
}
response = requests.get('https://api.example.com/articles', params=params)

# Method 2: In URL (not recommended for complex parameters)
response = requests.get('https://api.example.com/articles?page=1&limit=10')
```

### Custom Headers
```python
import requests

headers = {
    'User-Agent': 'MyApp/1.0',
    'Accept': 'application/json',
    'Content-Type': 'application/json'
}

response = requests.get('https://api.example.com/data', headers=headers)
```

## Real-World Examples

### Example 1: Weather API
```python
import requests

def get_weather(city, api_key):
    """Get current weather for a city using OpenWeatherMap API"""
    base_url = "http://api.openweathermap.org/data/2.5/weather"
    
    params = {
        'q': city,
        'appid': api_key,
        'units': 'metric'
    }
    
    try:
        response = requests.get(base_url, params=params)
        response.raise_for_status()
        
        weather_data = response.json()
        
        return {
            'city': weather_data['name'],
            'temperature': weather_data['main']['temp'],
            'description': weather_data['weather'][0]['description'],
            'humidity': weather_data['main']['humidity']
        }
    
    except requests.exceptions.RequestException as e:
        print(f"Error fetching weather data: {e}")
        return None

# Usage (you need to get a free API key from OpenWeatherMap)
# weather = get_weather('London', 'your-api-key')
# if weather:
#     print(f"Weather in {weather['city']}: {weather['temperature']}°C, {weather['description']}")
```

### Example 2: GitHub API
```python
import requests

def get_github_user(username):
    """Get GitHub user information"""
    url = f"https://api.github.com/users/{username}"
    
    try:
        response = requests.get(url)
        response.raise_for_status()
        
        user_data = response.json()
        
        return {
            'name': user_data.get('name', 'N/A'),
            'bio': user_data.get('bio', 'N/A'),
            'public_repos': user_data['public_repos'],
            'followers': user_data['followers'],
            'following': user_data['following']
        }
    
    except requests.exceptions.RequestException as e:
        print(f"Error fetching user data: {e}")
        return None

# Usage
user_info = get_github_user('octocat')
if user_info:
    print(f"Name: {user_info['name']}")
    print(f"Bio: {user_info['bio']}")
    print(f"Public Repos: {user_info['public_repos']}")
```

### Example 3: Creating a Simple API Client Class
```python
import requests
import time

class APIClient:
    def __init__(self, base_url, api_key=None):
        self.base_url = base_url.rstrip('/')
        self.session = requests.Session()
        
        if api_key:
            self.session.headers.update({'Authorization': f'Bearer {api_key}'})
    
    def get(self, endpoint, params=None):
        """Make a GET request"""
        url = f"{self.base_url}/{endpoint.lstrip('/')}"
        return self._make_request('GET', url, params=params)
    
    def post(self, endpoint, data=None):
        """Make a POST request"""
        url = f"{self.base_url}/{endpoint.lstrip('/')}"
        return self._make_request('POST', url, json=data)
    
    def _make_request(self, method, url, **kwargs):
        """Make HTTP request with error handling and rate limiting"""
        try:
            response = self.session.request(method, url, **kwargs)
            response.raise_for_status()
            return response.json()
        
        except requests.exceptions.HTTPError as e:
            if response.status_code == 429:  # Rate limit
                print("Rate limit exceeded. Waiting...")
                time.sleep(60)
                return self._make_request(method, url, **kwargs)
            else:
                print(f"HTTP error: {e}")
                return None
        
        except requests.exceptions.RequestException as e:
            print(f"Request error: {e}")
            return None

# Usage
client = APIClient('https://jsonplaceholder.typicode.com')
posts = client.get('/posts')
if posts:
    print(f"Retrieved {len(posts)} posts")
```

## Best Practices

### 1. Use Sessions for Multiple Requests
```python
import requests

# Good: Reuse connection
session = requests.Session()
session.headers.update({'User-Agent': 'MyApp/1.0'})

for i in range(10):
    response = session.get(f'https://api.example.com/data/{i}')
    # Process response

session.close()
```

### 2. Set Timeouts
```python
import requests

# Always set timeouts to prevent hanging
response = requests.get('https://api.example.com/data', timeout=10)
```

### 3. Handle Rate Limits
```python
import requests
import time

def api_request_with_retry(url, max_retries=3):
    for attempt in range(max_retries):
        try:
            response = requests.get(url)
            
            if response.status_code == 429:  # Rate limit
                wait_time = int(response.headers.get('Retry-After', 60))
                print(f"Rate limited. Waiting {wait_time} seconds...")
                time.sleep(wait_time)
                continue
            
            response.raise_for_status()
            return response.json()
        
        except requests.exceptions.RequestException as e:
            if attempt == max_retries - 1:
                raise e
            time.sleep(2 ** attempt)  # Exponential backoff
    
    return None
```

### 4. Use Environment Variables for Secrets
```python
import os
import requests

# Good: Store API keys in environment variables
api_key = os.getenv('API_KEY')
if not api_key:
    raise ValueError("API_KEY environment variable not set")

headers = {'Authorization': f'Bearer {api_key}'}
response = requests.get('https://api.example.com/data', headers=headers)
```

### 5. Validate and Sanitize Data
```python
import requests
from typing import Optional, Dict, Any

def get_user_data(user_id: int) -> Optional[Dict[str, Any]]:
    """Get user data with proper validation"""
    if not isinstance(user_id, int) or user_id <= 0:
        raise ValueError("user_id must be a positive integer")
    
    response = requests.get(f'https://jsonplaceholder.typicode.com/users/{user_id}')
    
    if response.status_code == 404:
        return None
    
    response.raise_for_status()
    user_data = response.json()
    
    # Validate required fields
    required_fields = ['id', 'name', 'email']
    for field in required_fields:
        if field not in user_data:
            raise ValueError(f"Missing required field: {field}")
    
    return user_data
```

## Testing APIs

### 1. Testing with Python's unittest
```python
import unittest
import requests
from unittest.mock import patch, Mock

class TestAPIClient(unittest.TestCase):
    
    @patch('requests.get')
    def test_successful_api_call(self, mock_get):
        # Mock the API response
        mock_response = Mock()
        mock_response.status_code = 200
        mock_response.json.return_value = {'id': 1, 'title': 'Test Post'}
        mock_get.return_value = mock_response
        
        # Test your function
        result = get_post(1)  # Your API function
        
        # Assertions
        self.assertEqual(result['id'], 1)
        self.assertEqual(result['title'], 'Test Post')
        mock_get.assert_called_once_with('https://api.example.com/posts/1')
    
    @patch('requests.get')
    def test_api_error_handling(self, mock_get):
        # Mock an error response
        mock_response = Mock()
        mock_response.status_code = 404
        mock_response.raise_for_status.side_effect = requests.exceptions.HTTPError()
        mock_get.return_value = mock_response
        
        # Test error handling
        result = get_post(999)
        self.assertIsNone(result)

if __name__ == '__main__':
    unittest.main()
```

### 2. Testing with pytest
```python
import pytest
import requests
from unittest.mock import Mock, patch

def test_api_success():
    with patch('requests.get') as mock_get:
        mock_response = Mock()
        mock_response.status_code = 200
        mock_response.json.return_value = {'data': 'test'}
        mock_get.return_value = mock_response
        
        result = your_api_function()
        assert result['data'] == 'test'

def test_api_timeout():
    with patch('requests.get') as mock_get:
        mock_get.side_effect = requests.exceptions.Timeout()
        
        result = your_api_function()
        assert result is None
```

### 3. Integration Testing
```python
import requests
import pytest

class TestRealAPI:
    """Test against real API endpoints (use with caution)"""
    
    def test_public_api(self):
        response = requests.get('https://jsonplaceholder.typicode.com/posts/1')
        assert response.status_code == 200
        
        data = response.json()
        assert 'id' in data
        assert 'title' in data
        assert 'body' in data
    
    def test_api_error_codes(self):
        response = requests.get('https://jsonplaceholder.typicode.com/posts/999999')
        assert response.status_code == 404
```

## Resources for Further Learning

### Official Documentation
- [Python Requests Documentation](https://docs.python-requests.org/)
- [HTTP Status Codes](https://httpstatuses.com/)
- [REST API Tutorial](https://restfulapi.net/)

### Free APIs for Practice
- [JSONPlaceholder](https://jsonplaceholder.typicode.com/) - Fake REST API for testing
- [OpenWeatherMap](https://openweathermap.org/api) - Weather data API
- [GitHub API](https://docs.github.com/en/rest) - Access GitHub data
- [Dog API](https://dog.ceo/dog-api/) - Random dog pictures
- [JSON API](https://jsonapi.org/) - Public APIs directory

### Tools for API Development
- **Postman**: GUI for testing APIs
- **curl**: Command-line tool for HTTP requests
- **HTTPie**: User-friendly command-line HTTP client
- **Insomnia**: REST client for API testing

### Advanced Topics to Explore
- **Async API calls** with `asyncio` and `aiohttp`
- **GraphQL** APIs with `gql` or `graphene`
- **API rate limiting** and **caching strategies**
- **OAuth 2.0** authentication flows
- **Webhook** handling and processing
- **API versioning** strategies
- **Building your own APIs** with FastAPI or Flask

### Books and Courses
- "RESTful Web APIs" by Leonard Richardson
- "Building APIs with Node.js" by Caio Ribeiro Pereira
- "Python Requests Essentials" by Rakesh Vidya Chandra

## Practice Exercises

### Beginner Exercises
1. Make a GET request to fetch a random joke from a joke API
2. Create a weather checker that takes a city name and displays current weather
3. Build a currency converter using a free exchange rate API
4. Fetch and display trending repositories from GitHub API

### Intermediate Exercises
1. Create a news aggregator that fetches articles from multiple sources
2. Build a social media scheduler that posts to multiple platforms
3. Create a stock price tracker with alerts
4. Build a URL shortener service client

### Advanced Exercises
1. Implement OAuth 2.0 flow for a real service
2. Create an API wrapper class with full error handling and retry logic
3. Build a real-time data dashboard using WebSocket APIs
4. Implement API caching with Redis

## Conclusion

Congratulations! You now have a solid foundation in working with APIs using Python. Remember:

- **Start simple**: Begin with GET requests to public APIs
- **Handle errors gracefully**: Always implement proper error handling
- **Respect rate limits**: Don't overwhelm APIs with too many requests
- **Read documentation**: Each API is different, always check the docs
- **Practice regularly**: Build small projects to reinforce your learning

The world of APIs is vast and exciting. With these fundamentals, you're ready to integrate any service into your Python applications and build amazing things!

Happy coding! 🐍🚀
