# 🗺️ Camino AI - Location Intelligence for AI Agents

## Overview

This tutorial series demonstrates how to build location-aware AI agents using **Camino AI**, a powerful location intelligence API that provides real-world grounding for AI systems. Unlike traditional web search tools, Camino specializes in spatial reasoning, natural language place search, and journey planning—essential capabilities for agents that need to understand and navigate the physical world.

## 🎯 What is Camino AI?

Camino AI is a location intelligence platform designed specifically for AI agents, offering:

- **Natural Language Understanding**: Convert queries like "quiet cafes for working" into structured location data
- **Spatial Intelligence**: Calculate routes, distances, and spatial relationships
- **Context-Aware Search**: Understand intent and context (e.g., "romantic dinner" vs "business lunch")
- **Journey Planning**: Optimize multi-stop trips with constraints
- **Cost-Effective**: $0.001 per call with a generous free tier
- **AI-First Design**: Built for agents, not traditional mapping applications

## 📚 Tutorials in This Series

### 1. [search-query-context.ipynb](./search-query-context.ipynb)
**Foundation Tutorial - Getting Started with Camino AI**

Learn the fundamentals of Camino AI and explore its core capabilities:
- Setting up authentication and API clients
- Natural language location search with AI ranking
- Calculating spatial relationships and routes
- Understanding location context and surroundings
- Planning complex journeys with multiple waypoints
- MCP (Model Context Protocol) integration

**Best For**: Developers new to Camino AI who want to understand its capabilities

### 2. [langchain-agent-tutorial.ipynb](./langchain-agent-tutorial.ipynb)
**Advanced Tutorial - Building Production Agents with LangChain**

Build sophisticated location-aware agents using LangChain and Camino AI:
- Setting up Camino tools with LangChain integration
- Creating ReAct agents for location reasoning
- Implementing agents with memory for personalization
- Combining multiple tools (location + web search)
- Real-world use cases (real estate, travel, logistics)
- Production deployment considerations
- Error handling, caching, and monitoring

**Best For**: Developers building production AI agents that need location intelligence

## 🚀 Quick Start

### Prerequisites

1. **Get API Keys**:
   - Sign up for Camino AI at [app.getcamino.ai](https://app.getcamino.ai?utm_source=agents-towards-p
       roduction&utm_medium=github&utm_campaign=tutorial)
   - Get an OpenAI API key (or another LLM provider)

2. **Install Dependencies**:
```bash
pip install langchain langchain-openai langchain-camino python-dotenv requests
```

3. **Set Environment Variables**:
```bash
# Create .env file
echo "CAMINO_API_KEY=your-camino-api-key" >> .env
echo "OPENAI_API_KEY=your-openai-api-key" >> .env
```

### Basic Usage Example

```python
from langchain_camino import CaminoSearch
import os

# Initialize Camino search tool
camino = CaminoSearch(api_key=os.environ["CAMINO_API_KEY"])

# Find coffee shops
results = camino.invoke({
    "query": "coffee shops with wifi for remote work",
    "latitude": 40.7589,
    "longitude": -73.9851,
    "limit": 5
})

# Display results
for place in results['places']:
    print(f"{place['name']} - {place['distance']}m away")
```

## 🛠️ Key Features Demonstrated

### Natural Language Search
```python
# Understands context and intent
"quiet coffee shops for working"
"romantic restaurants with outdoor seating"
"family-friendly parks with playgrounds"
```

### Spatial Intelligence
```python
# Calculate relationships between locations
relationship = camino.relationship(
    start={"lat": 40.7589, "lon": -73.9851},
    end={"lat": 40.7829, "lon": -73.9654}
)
# Returns: distance, direction, travel_time, description
```

### Journey Planning
```python
# Optimize multi-stop trips
journey = camino.journey(
    waypoints=[
        {"query": "Times Square", "duration": 30},
        {"query": "Central Park", "duration": 60},
        {"query": "Museum of Modern Art", "duration": 120}
    ],
    transport_mode="walking"
)
```

### Context-Aware Results
```python
# Different contexts return different results
camino.query(
    query="restaurants",
    context="business lunch meeting"  # Returns professional venues
)

camino.query(
    query="restaurants", 
    context="romantic anniversary dinner"  # Returns intimate spots
)
```

## 🤖 Agent Architecture Examples

### Simple ReAct Agent
```python
from langgraph.prebuilt import create_react_agent

location_agent = create_react_agent(
    model=llm,
    tools=[camino_search, camino_query, camino_journey],
    prompt=location_prompt
)
```

### Agent with Memory
```python
from langchain.memory import ConversationBufferMemory

memory = ConversationBufferMemory()
agent_with_memory = create_agent(
    tools=[camino_tools],
    memory=memory  # Remembers user preferences
)
```

### Multi-Tool Agent
```python
# Combine location intelligence with web search
multi_agent = create_react_agent(
    tools=[
        camino_search,    # Find places
        web_search,       # Get reviews/details
        database_lookup   # Check internal data
    ]
)
```

## 💡 Use Cases

### 🏡 Real Estate
- Analyze neighborhood quality and amenities
- Calculate commute times to work locations
- Find schools, parks, and shopping nearby
- Generate location scores for properties

### ✈️ Travel & Tourism
- Create personalized itineraries
- Find attractions matching interests
- Optimize sightseeing routes
- Discover local hidden gems

### 📦 Logistics & Delivery
- Optimize delivery routes
- Calculate service areas
- Plan multi-stop journeys
- Estimate travel times

### 🍔 Food & Dining
- Find restaurants by cuisine and ambiance
- Discover dietary-specific options
- Plan food tours
- Locate late-night options

### 🚨 Emergency Services
- Find nearest hospitals/police/fire stations
- Calculate response times
- Identify access points
- Provide area context for responders

## 🔌 Integration Options

### MCP (Model Context Protocol)
```json
{
  "mcpServers": {
    "camino-ai": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-http",
        "https://mcp.getcamino.ai/mcp?caminoApiKey=YOUR_KEY"
      ]
    }
  }
}
```

### Direct API
```python
import requests

response = requests.get(
    "https://api.getcamino.ai/query",
    params={"query": "coffee shops", "lat": 40.7589, "lon": -73.9851},
    headers={"X-API-Key": "your-api-key"}
)
```

### LangChain Integration
```python
from langchain_camino import CaminoSearch, CaminoQuery, CaminoJourney

tools = [
    CaminoSearch(),
    CaminoQuery(),
    CaminoJourney()
]
```

## 📈 Performance & Optimization

### Best Practices
1. **Cache Results**: Implement caching for repeated queries
2. **Batch Requests**: Combine related searches when possible
3. **Set Defaults**: Configure default locations and radius
4. **Handle Errors**: Implement retry logic with exponential backoff
5. **Monitor Usage**: Track API calls and response times

### Example Production Pattern
```python
class ProductionLocationAgent:
    def __init__(self):
        self.cache = {}
        self.default_location = {"lat": 40.7589, "lon": -73.9851}
    
    async def search_with_cache(self, query):
        cache_key = f"{query}:{self.default_location}"
        if cache_key in self.cache:
            return self.cache[cache_key]
        
        result = await self.camino.search(query)
        self.cache[cache_key] = result
        return result
```

## 🚦 Getting Help

### Resources
- 📚 [API Documentation](https://api.getcamino.ai/docs)
- 🎮 [Interactive Playground](https://app.getcamino.ai/playground?utm_source=agents-towards-p
       roduction&utm_medium=github&utm_campaign=tutorial)

### Common Issues

**Issue**: API key not working
```python
# Solution: Check environment variable
import os
print(f"API Key set: {'CAMINO_API_KEY' in os.environ}")
```

**Issue**: No results returned
```python
# Solution: Verify coordinates are correct
# NYC: lat=40.7589, lon=-73.9851
# SF: lat=37.7749, lon=-122.4194
```

**Issue**: Rate limiting
```python
# Solution: Implement exponential backoff
import time
for attempt in range(3):
    try:
        result = camino.search(query)
        break
    except RateLimitError:
        time.sleep(2 ** attempt)
```

## 🎓 Learning Path

1. **Start Here**: Complete `search-query-context.ipynb` to understand Camino basics
2. **Build Agents**: Work through `langchain-agent-tutorial.ipynb` for production patterns
3. **Experiment**: Try different use cases and agent architectures
4. **Deploy**: Use the production patterns in real applications
5. **Contribute**: Share your innovations with the community

## 📝 License

These tutorials are provided as educational resources. Please refer to Camino AI's terms of service for API usage guidelines.

## 🤝 Contributing

We welcome contributions! If you've built interesting location-aware agents or have improvements to suggest:

1. Fork the repository
2. Create your feature branch
3. Commit your changes
4. Push to the branch
5. Open a Pull Request

## 🌟 Why Camino AI?

In a world where AI agents are becoming increasingly sophisticated, the ability to understand and reason about physical locations is crucial. Camino AI bridges the gap between the digital intelligence of LLMs and the physical world we live in.

Whether you're building a travel assistant, a real estate analyzer, a delivery optimizer, or any other location-aware application, Camino AI provides the spatial intelligence your agents need to be truly helpful.

**Ready to give your AI agents a sense of place?** 🚀

---

*Built with ❤️ for the AI agent community*