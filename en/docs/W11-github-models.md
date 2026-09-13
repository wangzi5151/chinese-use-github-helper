# GitHub Models AI/ML Platform

## What is GitHub Models?

GitHub Models is an AI model platform provided by GitHub, allowing you to use and test various AI models directly on GitHub.

## Supported Models

| Model | Provider | Use Case |
|-------|----------|----------|
| GPT-4o | OpenAI | General conversation, code generation |
| GPT-4o-mini | OpenAI | Lightweight tasks |
| Claude 3.5 Sonnet | Anthropic | Code analysis, conversation |
| Llama 3.1 | Meta | Open source general model |
| Mistral | Mistral AI | European open source model |
| Phi-3 | Microsoft | Lightweight model |
| GitLab Foundation | GitLab | Code-related tasks |

## Quick Start

### Use via API

```bash
# Set API Key
export GITHUB_TOKEN="your-github-token"

# Call GPT-4o
curl -X POST "https://models.inference.ai.azure.com/chat/completions" \
  -H "Authorization: Bearer $GITHUB_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-4o",
    "messages": [
      {"role": "user", "content": "Hello, who are you?"}
    ]
  }'
```

### Use Python SDK

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://models.inference.ai.azure.com",
    api_key=os.environ["GITHUB_TOKEN"],
)

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Write a Python function to sort a list"}
    ]
)

print(response.choices[0].message.content)
```

### Use in GitHub Actions

```yaml
# .github/workflows/ai-review.yml
name: AI Code Review

on:
  pull_request:

permissions:
  contents: read
  pull-requests: write

jobs:
  ai-review:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    
    - name: Get PR diff
      id: diff
      run: |
        DIFF=$(gh pr diff ${{ github.event.pull_request.number }})
        echo "diff<<EOF" >> $GITHUB_OUTPUT
        echo "$DIFF" >> $GITHUB_OUTPUT
        echo "EOF" >> $GITHUB_OUTPUT
    
    - name: AI Review
      run: |
        curl -X POST "https://models.inference.ai.azure.com/chat/completions" \
          -H "Authorization: Bearer ${{ secrets.GITHUB_TOKEN }}" \
          -H "Content-Type: application/json" \
          -d '{
            "model": "gpt-4o",
            "messages": [
              {"role": "system", "content": "Review this code change and provide feedback on bugs, improvements, and best practices."},
              {"role": "user", "content": "${{ steps.diff.outputs.diff }}"}
            ]
          }'
```

## Usage Scenarios

### 1. Automatic Code Review

```python
def ai_code_review(diff: str) -> str:
    """Use AI to review code changes"""
    prompt = f"""
    Review the following code changes, provide:
    1. Potential bugs
    2. Performance issues
    3. Security vulnerabilities
    4. Code style suggestions
    
    Code changes:
    {diff}
    """
    
    response = client.chat.completions.create(
        model="gpt-4o",
        messages=[{"role": "user", "content": prompt}]
    )
    
    return response.choices[0].message.content
```

### 2. Automatic Documentation Generation

```python
def generate_docs(code: str) -> str:
    """Generate documentation for code"""
    prompt = f"""
    Generate detailed API documentation for the following code, including:
    1. Function description
    2. Parameter descriptions
    3. Return value
    4. Usage examples
    
    Code:
    {code}
    """
    
    response = client.chat.completions.create(
        model="gpt-4o",
        messages=[{"role": "user", "content": prompt}]
    )
    
    return response.choices[0].message.content
```

### 3. Test Case Generation

```python
def generate_tests(code: str) -> str:
    """Generate test cases for code"""
    prompt = f"""
    Generate complete test cases for the following code, including:
    1. Normal case tests
    2. Boundary case tests
    3. Error case tests
    
    Use pytest framework.
    
    Code:
    {code}
    """
    
    response = client.chat.completions.create(
        model="gpt-4o",
        messages=[{"role": "user", "content": prompt}]
    )
    
    return response.choices[0].message.content
```

### 4. Natural Language to SQL

```python
def nl_to_sql(question: str, schema: str) -> str:
    """Convert natural language to SQL"""
    prompt = f"""
    Based on the following database schema, convert the natural language question to SQL query.
    
    Schema:
    {schema}
    
    Question: {question}
    """
    
    response = client.chat.completions.create(
        model="gpt-4o",
        messages=[{"role": "user", "content": prompt}]
    )
    
    return response.choices[0].message.content
```

## Pricing

| Model | Per 1000 Input Tokens | Per 1000 Output Tokens |
|-------|----------------------|----------------------|
| GPT-4o | $0.005 | $0.015 |
| GPT-4o-mini | $0.00015 | $0.0006 |
| Claude 3.5 Sonnet | $0.003 | $0.015 |
| Llama 3.1 | Free | Free |

## Best Practices

1. **Choose Appropriate Model**: Select model based on task complexity
2. **Optimize Prompts**: Clear prompts get better results
3. **Control Costs**: Monitor API usage
4. **Error Handling**: Handle API call failures
5. **Cache Results**: Cache identical requests to reduce calls

## FAQ

### Q: What's the difference between GitHub Models and GitHub Copilot?
A: GitHub Models is an API platform, can directly call models; Copilot is an AI assistant integrated in IDE.

### Q: How to improve API call speed?
A: Use streaming responses, batch requests, cache results.

### Q: Is model-generated content accurate?
A: Model-generated content may be inaccurate, recommend human review, especially for critical tasks.

## Related Resources

- [GitHub Models Documentation](https://docs.github.com/en/github-models)
- [Available Models](https://docs.github.com/en/github-models/available-models)
- [API Reference](https://docs.github.com/en/github-models/reference)