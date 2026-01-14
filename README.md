# Spring AI Learning - Quick Start Guide

Get started with Spring AI in 5 minutes!

## Prerequisites Checklist

- [ ] Java 17+ installed (`java -version`)
- [ ] Maven 3.8+ installed (`mvn -version`)
- [ ] Docker installed and running (`docker --version`)
- [ ] OpenAI API Key (get from https://platform.openai.com/api-keys)
- [ ] Git installed (optional)

## Step 1: Setup Project (2 minutes)

### Option A: Clone Repository
```bash
git clone 
cd spring-ai-learning
```

### Option B: Create from Scratch
```bash
# Create project directory
mkdir spring-ai-learning
cd spring-ai-learning

# Copy the provided pom.xml, application.yml, and source files
```

## Step 2: Configure Environment (1 minute)

### Set OpenAI API Key
```bash
# Linux/Mac
export OPENAI_API_KEY='your-api-key-here'

# Windows PowerShell
$env:OPENAI_API_KEY='your-api-key-here'

# Windows CMD
set OPENAI_API_KEY=your-api-key-here
```

### Create .env file (Alternative)
```bash
# Create .env file in project root
echo "OPENAI_API_KEY=your-api-key-here" > .env
```

## Step 3: Start Infrastructure (1 minute)

```bash
# Start PostgreSQL with pgvector
docker-compose up -d

# Verify services are running
docker-compose ps

# Expected output:
# NAME                  STATUS
# spring-ai-postgres    Up
# spring-ai-pgadmin     Up (optional)
# spring-ai-redis       Up (optional)
```

## Step 4: Build and Run (1 minute)

```bash
# Build the project
mvn clean install -DskipTests

# Run the application
mvn spring-boot:run

# Or run with specific profile
mvn spring-boot:run -Dspring-boot.run.profiles=dev
```

## Step 5: Test Your First API Call (30 seconds)

### Test Phase 1 - Token Estimation
```bash
curl "http://localhost:8080/api/phase1/tokens/estimate?text=Hello%20Spring%20AI"
```

**Expected Response:**
```json
{
  "text": "Hello Spring AI",
  "characters": 15,
  "estimatedTokens": 4
}
```

### Test Phase 2 - Simple Chat
```bash
curl -X POST http://localhost:8080/api/phase2/chat/simple \
  -H "Content-Type: text/plain" \
  -d "What is Spring AI?"
```

**Expected Response:**
```
Spring AI is a framework that provides abstractions for integrating AI models into Spring applications...
```

## Interactive API Testing

Open your browser and visit:
```
http://localhost:8080/swagger-ui.html
```

This provides an interactive UI to test all endpoints!

---

## Common Commands Cheat Sheet

### Project Management
```bash
# Build project
mvn clean install

# Run application
mvn spring-boot:run

# Run with dev profile
mvn spring-boot:run -Dspring-boot.run.profiles=dev

# Run tests
mvn test

# Package as JAR
mvn clean package
```

### Docker Management
```bash
# Start all services
docker-compose up -d

# Stop all services
docker-compose down

# View logs
docker-compose logs -f postgres

# Restart a service
docker-compose restart postgres

# Remove all data (CAUTION!)
docker-compose down -v
```

### Database Access
```bash
# Connect to PostgreSQL
docker exec -it spring-ai-postgres psql -U postgres -d vectordb

# Inside psql:
\dt                    # List tables
\d+ vector_store      # Describe vector_store table
SELECT COUNT(*) FROM vector_store;  # Count vectors
```

---

## Testing Each Phase

### Phase 1: Foundation
```bash
# Estimate tokens
curl "http://localhost:8080/api/phase1/tokens/estimate?text=Test%20message"

# Analyze prompt quality
curl -X POST http://localhost:8080/api/phase1/prompts/analyze \
  -H "Content-Type: text/plain" \
  -d "Explain machine learning in 100 words"
```

### Phase 2: Spring AI Basics
```bash
# Simple chat
curl -X POST http://localhost:8080/api/phase2/chat/simple \
  -H "Content-Type: text/plain" \
  -d "Hello!"

# Streaming (watch real-time response)
curl -N -X POST http://localhost:8080/api/phase2/chat/stream \
  -H "Content-Type: text/plain" \
  -d "Tell me a short story"

# Structured output
curl "http://localhost:8080/api/phase2/structured/recipe?dish=pancakes"
```

### Phase 3: Vectors and RAG
```bash
# Store a document
curl -X POST http://localhost:8080/api/phase3/vectors/store \
  -H "Content-Type: application/json" \
  -d '{
    "content": "Spring AI provides AI integration for Spring applications",
    "category": "documentation",
    "source": "spring-ai-docs"
  }'

# Semantic search
curl "http://localhost:8080/api/phase3/search/semantic?query=AI%20framework&topK=3"

# RAG query
curl -X POST http://localhost:8080/api/phase3/rag/query \
  -H "Content-Type: application/json" \
  -d '{"question": "What is Spring AI?", "topK": 3}'
```

### Phase 4: Advanced Features
```bash
# Function calling
curl -X POST http://localhost:8080/api/phase4/function/weather \
  -H "Content-Type: text/plain" \
  -d "What's the weather in London?"

# Image analysis (save an image as test.png first)
curl -X POST http://localhost:8080/api/phase4/multimodal/image/analyze \
  -F "file=@test.png" \
  -F "prompt=Describe this image"
```

### Phase 5: Production Ready
```bash
# Secure chat
curl -X POST http://localhost:8080/api/phase5/secure/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello, how are you?"}'

# Content moderation
curl -X POST http://localhost:8080/api/phase5/secure/moderate \
  -H "Content-Type: text/plain" \
  -d "This is a test message"
```

---

## Setting Up Ollama (Optional - Local Models)

### Install Ollama
```bash
# macOS
brew install ollama

# Linux
curl -fsSL https://ollama.com/install.sh | sh

# Windows
# Download from https://ollama.com/download
```

### Download Models
```bash
# Download Llama 3.2
ollama pull llama3.2

# Download Mistral
ollama pull mistral

# List installed models
ollama list
```

### Start Ollama Server
```bash
# Start server
ollama serve

# Test in another terminal
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.2",
  "prompt": "Why is the sky blue?"
}'
```

### Test with Spring AI
```bash
curl -X POST http://localhost:8080/api/phase5/ollama/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello from local model!"}'
```

---

## Troubleshooting

### Problem: OpenAI API returns 401 Unauthorized
**Solution:**
```bash
# Verify API key is set
echo $OPENAI_API_KEY

# Re-export if needed
export OPENAI_API_KEY='your-actual-key'

# Restart application
mvn spring-boot:run
```

### Problem: Cannot connect to PostgreSQL
**Solution:**
```bash
# Check if Docker is running
docker ps

# Restart PostgreSQL
docker-compose restart postgres

# Check logs
docker-compose logs postgres
```

### Problem: Port 8080 already in use
**Solution:**
```bash
# Change port in application.yml
server:
  port: 8081

# Or set via command line
mvn spring-boot:run -Dserver.port=8081
```

### Problem: Maven build fails
**Solution:**
```bash
# Clean and rebuild
mvn clean install -U

# Skip tests if needed
mvn clean install -DskipTests

# Update dependencies
mvn dependency:purge-local-repository
```

---

## Monitoring and Health Checks

### Application Health
```bash
# Check application health
curl http://localhost:8080/actuator/health

# Check detailed metrics
curl http://localhost:8080/actuator/metrics
```

### Database Health
```bash
# Connect to database
docker exec -it spring-ai-postgres psql -U postgres -d vectordb

# Check vector extension
SELECT * FROM pg_extension WHERE extname = 'vector';

# View stored vectors
SELECT id, metadata, LENGTH(embedding::text) as embedding_size 
FROM vector_store 
LIMIT 5;
```

---

## Next Steps

1. **Explore Swagger UI**: Visit http://localhost:8080/swagger-ui.html for interactive API docs
2. **Read Full README**: Check README.md for detailed theory and examples
3. **Run Tests**: Execute `mvn test` to see integration tests
4. **Customize**: Modify controllers and add your own use cases
5. **Deploy**: Follow deployment guide for production deployment

## Learning Path

1. ✅ **Day 1**: Understand Phase 1 (Foundation) and Phase 2 (Basics)
2. ✅ **Day 2**: Master Phase 3 (Vectors and RAG)
3. ✅ **Day 3**: Explore Phase 4 (Advanced Features)
4. ✅ **Day 4**: Implement Phase 5 (Production Ready)
5. ✅ **Day 5**: Build your own AI application!

## Resources

- 📚 [Spring AI Documentation](https://docs.spring.io/spring-ai/reference/)
- 🎥 [Spring AI YouTube Channel](https://www.youtube.com/SpringSourceDev)
- 💬 [Spring AI Discord Community](https://discord.gg/spring)
- 📖 [OpenAI API Reference](https://platform.openai.com/docs)

---

## Support

Having issues? Check:
1. Application logs in `logs/spring-ai-learning.log`
2. Docker logs: `docker-compose logs -f`
3. GitHub Issues section
4. Spring AI documentation

Happy Learning! 🚀
