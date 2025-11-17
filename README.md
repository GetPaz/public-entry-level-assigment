# API Integration Assignment: Weather-Based Task Manager

**Time Limit: 1 Day** | **Level: Fresher/Junior Developer**

## Project Overview
Build a simple task management system that automatically adjusts task priorities and sends notifications based on weather conditions. For example, "outdoor tasks" should be rescheduled when bad weather is detected.

## Core Requirements (Must Complete)

### 1. API Integrations (2 Required)
1. [**Weather API**](https://openweathermap.org/api/one-call-3) (Required):
   - Use OpenWeatherMap API (free tier)
   - Fetch current weather and 5-day forecast
   - Store relevant data (temperature, conditions, wind speed)

2. **Choose ONE of the following:**
   - **IP Geolocation API** (ipapi.co or ip-api.com) - to auto-detect user location
   - **Holiday API** (date.nager.at) - to check if task date falls on holiday
   - **Time Zone API** (worldtimeapi.org) - to handle tasks across time zones

### 2. Task State Machine
Implement a simple state machine for tasks with these states:

```
DRAFT → SCHEDULED → IN_PROGRESS → COMPLETED
         ↓            ↓
      WEATHER_DELAYED → RESCHEDULED
```

**State Rules:**
- Tasks move from `SCHEDULED` to `WEATHER_DELAYED` if weather conditions are unfavorable
- `WEATHER_DELAYED` tasks auto-transition to `RESCHEDULED` with a new date
- Tasks can be manually moved to `IN_PROGRESS` and `COMPLETED`

### 3. Business Logic Implementation

#### Weather Impact Calculator
Create a function that determines if weather affects a task based on:

**Task Categories:**
- `outdoor` - Affected by rain, snow, high wind (>25 mph)
- `delivery` - Affected by severe weather only (storm, heavy snow)
- `indoor` - Not affected by weather
- `travel` - Affected by visibility and severe conditions

**Return format:**
```json
{
  "canProceed": true/false,
  "reason": "Heavy rain expected",
  "suggestedDate": "2024-01-15",
  "riskLevel": "low|medium|high"
}
```

### 4. Data Management
- Store tasks in memory (no database required)
- Each task should have:
  - ID, title, description, category, scheduledDate, location, state, priority
- Implement CRUD operations for tasks
- Cache weather data for 10 minutes to avoid excessive API calls

## Technical Specifications

### Technology Stack
- **Language**: Python 3.8+
- **Framework**: Flask or FastAPI (choose one)
- **HTTP Client**: `requests` or `httpx`
- **Testing**: `pytest`
- **Optional**: `pydantic` for data validation

### Required Endpoints
Create a REST API with these endpoints:

1. **Tasks**
   - `POST /tasks` - Create a task
   - `GET /tasks` - List all tasks with weather impact status
   - `GET /tasks/:id` - Get single task with weather details
   - `PUT /tasks/:id/state` - Update task state
   - `DELETE /tasks/:id` - Delete task

2. **Weather Check**
   - `GET /tasks/:id/weather-impact` - Check if weather affects specific task
   - `POST /tasks/bulk-weather-check` - Check weather impact for all scheduled tasks

3. **System**
   - `GET /health` - API health check
   - `GET /state-machine` - Return state machine configuration

### Python-Specific Requirements
- Use type hints for function parameters and returns
- Implement proper Python classes for models
- Use `@dataclass` or Pydantic models for data structures
- Handle exceptions with try/except blocks
- Use `logging` module instead of print statements

### Error Handling
- Handle API failures gracefully (return cached data or defaults)
- Validate all inputs
- Return appropriate HTTP status codes
- Include error messages in response

## Deliverables

### 1. Working Application
- Complete REST API with all required endpoints
- Simple state machine implementation
- Weather integration working

### 2. Documentation
- **README.md** with:
  - How to run the project
  - API endpoints with curl examples
  - Environment variables needed
  - Brief explanation of your weather impact logic

### 3. Test Coverage
- Write at least 5 unit tests using `pytest`
- Include one integration test for the state machine
- Test file example: `test_weather_impact.py`

### 4. Sample Data
Provide a Python script (`seed_data.py`) to populate sample tasks:
- At least 5 tasks with different categories
- Different scheduled dates (today, tomorrow, next week)
- Different locations

## Sample Code Structure (To Get You Started)

### Example Model 
```python
from enum import Enum
from datetime import datetime
from typing import Optional
from dataclasses import dataclass

class TaskState(Enum):
    DRAFT = "draft"
    SCHEDULED = "scheduled"
    IN_PROGRESS = "in_progress"
    WEATHER_DELAYED = "weather_delayed"
    RESCHEDULED = "rescheduled"
    COMPLETED = "completed"

class TaskCategory(Enum):
    OUTDOOR = "outdoor"
    DELIVERY = "delivery"
    INDOOR = "indoor"
    TRAVEL = "travel"

@dataclass
class Task:
    id: str
    title: str
    description: str
    category: TaskCategory
    scheduled_date: datetime
    location: str
    state: TaskState
    priority: int
```

### Example Service Method
```python
from typing import Dict, Any
import requests
from datetime import datetime, timedelta

class WeatherService:
    def __init__(self, api_key: str):
        self.api_key = api_key
        self.base_url = "https://api.openweathermap.org/data/3.0"
        self.cache = {}
        self.cache_duration = timedelta(minutes=10)
    
    def get_weather_impact(self, task: Task) -> Dict[str, Any]:
        """
        Determine if weather affects a task
        Returns: dict with canProceed, reason, suggestedDate, riskLevel
        """
        # Your implementation here
        pass
```

## Evaluation Criteria

### Must Have (70%)
- All endpoints working correctly
- State machine handles transitions properly
- Weather API integration functional
- Clean, readable code with proper error handling
- Clean code/folder structure

### Good to Have (20%)
- Modular code structure
- Proper input validation
- Comprehensive error messages
- Basic unit tests

### Bonus Points (10%)
- Implement both optional APIs
- Add filtering/sorting to task list endpoint
- Include basic rate limiting
- Add a simple notification system (console logs are fine)

## Hints & Guidelines

### Getting Started with Python
1. Set up virtual environment: `python -m venv venv`
2. Activate it: `source venv/bin/activate` (Linux/Mac) or `venv\Scripts\activate` (Windows)
3. Install initial packages: `pip install flask requests python-dotenv`
4. Start with task CRUD operations
5. Add weather API integration
6. Implement state machine using Python Enums
7. Connect weather logic to state transitions
8. Add error handling with try/except blocks

### Python Best Practices
- Use `os.getenv()` or `python-dotenv` for environment variables
- Implement caching with a simple dictionary and timestamp checking
- Use `logging.getLogger(__name__)` for logging
- Type hints help catch errors: `def calculate_impact(task: Task) -> Dict[str, Any]:`

### Common Pitfalls to Avoid
- Don't hardcode API keys (use environment variables)
- Don't make API calls for every request (implement caching)
- Don't forget to handle API failures
- Don't overcomplicate the state machine

### What We're Looking For
- **Problem Solving**: How you handle the weather-task relationship
- **Code Organization**: Clear separation of concerns
- **API Design**: RESTful principles and consistent responses
- **Error Handling**: Graceful handling of edge cases
- **Understanding**: Comments explaining complex logic decisions

## Resources
- OpenWeatherMap API Docs: https://openweathermap.org/api
- REST API Best Practices: Research on your own
- State Machine Patterns: Research on your own

## Submission
- Push code to a Git repository (GitHub/GitLab)
- Include `.env.example` file with required environment variables
- Include `requirements.txt` with all dependencies
- Ensure the project runs with:
  ```bash
  pip install -r requirements.txt
  python run.py
  # or
  uvicorn app.main:app --reload  # for FastAPI
  # or
  flask run  # for Flask
  ```

### Required Python Packages
Your `requirements.txt` should include (versions are flexible):
```
flask>=2.0.0  # or fastapi>=0.100.0
requests>=2.28.0
pytest>=7.0.0
python-dotenv>=1.0.0
# Add other dependencies as needed
```

**Note**: This assignment is designed to test your ability to integrate APIs, manage application state, and write clean code. Focus on making it work first, then improve code quality if time permits.
