# Google GenAI Python SDK Usage Guide for Cursor

## IMPORTANT: Use `google-genai` Package, NOT `google-generativeai`

When working with Google's Gemini API in Python, **always use the `google-genai` package**, not `google-generativeai`. The `google-generativeai` package is deprecated and being phased out.

## Correct Installation

```bash
pip install google-genai
```

**NOT:**
```bash
pip install google-generativeai  # ❌ DEPRECATED - Don't use this
```

## Correct Import and Usage Pattern

### Basic Setup
```python
import os
from google import genai
from dotenv import load_dotenv

load_dotenv()

# Configure the client
client = genai.Client(api_key=os.getenv("GEMINI_API_KEY"))
```

### Key Differences from google-generativeai

| google-generativeai (OLD/DEPRECATED) | google-genai (CORRECT) |
|---------------------------------------|------------------------|
| `import google.generativeai as genai` | `from google import genai` |
| `genai.configure(api_key=api_key)` | `client = genai.Client(api_key=api_key)` |
| `genai.GenerativeModel('model-name')` | `client.models.generate_content(model='model-name')` |
| Global configuration | Client-based configuration |

## Simple Example - Send and Receive Message

```python
import os
from google import genai
from dotenv import load_dotenv

# Load environment variables
load_dotenv()

# Initialize client (CORRECT WAY)
api_key = os.getenv("GEMINI_API_KEY")
if not api_key:
    raise ValueError("GEMINI_API_KEY environment variable not set")

client = genai.Client(api_key=api_key)

def send_message(message: str):
    """Send a message to Gemini and get a response"""
    try:
        # Send message to Gemini
        response = client.models.generate_content(
            model='gemini-2.0-flash-exp',
            contents=message
        )
        
        # Return the response text
        return response.text
    
    except Exception as e:
        return f"Error: {str(e)}"

# Example usage
if __name__ == "__main__":
    # Simple conversation
    user_message = "Hello! Can you explain what Python is?"
    
    print(f"User: {user_message}")
    
    # Get response from Gemini
    ai_response = send_message(user_message)
    
    print(f"Gemini: {ai_response}")
```

## Key API Differences

### Content Generation
```python
# ❌ OLD WAY (google-generativeai)
model = genai.GenerativeModel('gemini-pro')
response = model.generate_content(contents=prompt)

# ✅ NEW WAY (google-genai)
response = client.models.generate_content(
    model='gemini-2.0-flash-exp',
    contents=prompt
)
```

### Configuration
```python
# ❌ OLD WAY (google-generativeai)
genai.configure(api_key=api_key)

# ✅ NEW WAY (google-genai)
client = genai.Client(api_key=api_key)
```

### Streaming
```python
# ❌ OLD WAY (google-generativeai)
response = model.generate_content(contents=prompt, stream=True)

# ✅ NEW WAY (google-genai)
response = client.models.generate_content(
    model='gemini-2.0-flash-exp',
    contents=prompt,
    config={'stream': True}
)
```

## Available Models (as of 2025)
- `gemini-2.0-flash-exp` (recommended for most use cases)
- `gemini-1.5-pro`
- `gemini-1.5-flash`

## Error Handling Best Practices

```python
def safe_generate_content(client, prompt, model='gemini-2.0-flash-exp'):
    """Generate content with proper error handling"""
    try:
        response = client.models.generate_content(
            model=model,
            contents=prompt
        )
        
        if hasattr(response, 'text') and response.text:
            return response.text
        else:
            return "No response generated"
            
    except Exception as e:
        print(f"Generation failed: {e}")
        return f"Error: {str(e)}"
```

## Migration Checklist

When migrating from `google-generativeai` to `google-genai`:

1. ✅ Change import: `from google import genai`
2. ✅ Replace `genai.configure()` with `client = genai.Client()`
3. ✅ Update model calls to use `client.models.generate_content()`
4. ✅ Update configuration parameters (use `config` dict)
5. ✅ Test all functionality with the new API
6. ✅ Update requirements.txt/dependencies

## Common Issues and Solutions

**Issue**: `ModuleNotFoundError: No module named 'google.generativeai'`
**Solution**: Install `google-genai` instead: `pip install google-genai`

**Issue**: `genai.configure() not working`
**Solution**: Use client-based configuration: `client = genai.Client(api_key=api_key)`

**Issue**: Model names not working
**Solution**: Use current model names like `gemini-2.0-flash-exp`

Remember: Always use `google-genai` package and client-based configuration for new projects! a