# Copilot Instructions for Mini-RAG-Quickstart

## Project Overview

This is a Retrieval-Augmented Generation (RAG) implementation that integrates **Azure OpenAI** and **CosmosDB** into a **Microsoft Teams** channel. Users can ask questions about sample data uploaded into the database via Teams chat, and the system augments the LLM responses with stored facts.

### Architecture Components
- **Teams**: User interface for chat interactions
- **Logic App**: Shuttles messages between Teams chat and Azure Functions
- **Azure Function**: Python-based serverless function that reads from CosmosDB, creates prompts, and calls Azure OpenAI
- **CosmosDB**: Stores facts/data used to augment chat requests
- **Azure OpenAI**: Hosted LLM (GPT-3.5) for processing enhanced requests and generating responses

## Technology Stack

- **Language**: Python 3.10/3.11
- **Cloud Platform**: Microsoft Azure
- **Key Services**: Azure Functions, CosmosDB, Azure OpenAI, Logic Apps
- **Dependencies**: 
  - `azure-functions`: Azure Functions runtime
  - `openai`: Azure OpenAI Python SDK

## Coding Standards and Style Guidelines

### Python Style
- Follow PEP 8 Python style guidelines
- Use meaningful variable names that describe their purpose
- Keep functions focused on a single responsibility
- Use type hints where appropriate to improve code clarity

### Code Organization
- Azure Functions code resides in `src/azureFunction/`
- Shell scripts for setup and deployment are in `bin/`
- Sample data files are stored in `data/`
- Each Azure Function should have a clear, descriptive name (e.g., `AskQuestion`)

### Security Best Practices
- Never commit sensitive credentials (API keys, connection strings) to the repository
- Use environment variables for all configuration values
- Sanitize user input to prevent injection attacks (e.g., HTML tag removal)
- Use Azure Key Vault or Function App settings for secrets management

### Error Handling
- Always validate required inputs and return appropriate HTTP status codes (400, 500, etc.)
- Log informative error messages using the `logging` module
- Provide clear error messages to users when requests fail

## Testing Requirements

### Manual Testing
- Test Azure Functions locally using Azure Functions Core Tools before deployment
- Validate CosmosDB connections and data retrieval
- Test end-to-end flow through Teams channel after deployment
- Verify responses are accurate and properly formatted

### Testing Environment
- Use separate Azure resources (Function Apps, CosmosDB instances) for development and production
- Test with sample data from `data/cosmosdb-facts.txt` before using production data

## Documentation Expectations

### Code Documentation
- Add docstrings to functions explaining their purpose, parameters, and return values
- Include inline comments for complex logic or non-obvious implementations
- Document environment variables required for configuration

### README Updates
- Keep README.md up-to-date with any changes to setup or deployment procedures
- Document any new prerequisites or dependencies
- Include troubleshooting steps for common issues

### Configuration Documentation
- Document all environment variables in `bin/updateFNConfig.sh`
- Provide clear instructions for obtaining API keys and connection strings
- Document any changes to Azure resource configurations

## Architecture Patterns

### RAG Implementation Pattern
This project follows the Retrieval-Augmented Generation pattern:
1. **Data Collection**: Facts stored in CosmosDB (`data/cosmosdb-facts.txt`)
2. **User Query**: Question received via Teams channel
3. **Data Retrieval**: Azure Function reads all facts from CosmosDB
4. **Prompt Enhancement**: Facts are prepended to user question as system context
5. **LLM Processing**: Azure OpenAI generates response based on augmented prompt
6. **Response Delivery**: Answer returned to Teams via Logic App

### Function Bindings
- Use CosmosDB input bindings for efficient data retrieval
- Leverage Azure Functions HTTP triggers for RESTful endpoints
- Configure bindings in function decorators rather than separate configuration files

### Environment Configuration
- Store all configuration in environment variables
- Use `os.getenv()` with sensible defaults where appropriate
- Document required vs. optional configuration variables

## Deployment Process

### Local Development
1. Clone repository locally
2. Open `src/azureFunction` in VS Code
3. Install Azure Functions extension for VS Code
4. Test functions locally with Azure Functions Core Tools

### Deployment
1. Deploy using VS Code: `Ctrl-Shift-P` → "Azure Functions: Deploy to Function App"
2. Configure Function App settings using `bin/updateFNConfig.sh`
3. Verify deployment in Azure Portal
4. Test end-to-end functionality through Teams

## Clarifying Questions Policy

**Always ask clarifying questions before making significant changes:**
- If requirements are ambiguous, ask for specific examples or use cases
- When multiple implementation approaches are viable, present options and ask for preference
- If changes might affect existing functionality, confirm the scope of changes
- When security implications are unclear, ask about data sensitivity and compliance requirements
- If deployment targets are not specified (dev/staging/prod), ask before proceeding

## Additional Guidelines

### Dependencies
- Avoid adding unnecessary dependencies to `requirements.txt`
- Pin dependency versions for production deployments
- Test thoroughly after updating the OpenAI SDK or Azure Functions runtime

### Azure Functions Best Practices
- Keep functions stateless - don't rely on local state between invocations
- Optimize cold start times by minimizing imports and initialization
- Use appropriate Azure Functions pricing tiers based on usage patterns
- Monitor Application Insights for performance and error tracking

### CosmosDB Integration
- Use appropriate partition keys for scalable data access
- Leverage CosmosDB input bindings instead of manual SDK calls
- Consider consistency level requirements for your use case
- Monitor RU (Request Unit) consumption and optimize queries

### Prompt Engineering
- Keep system prompts clear and specific
- Include context about desired response format and tone
- Test prompts with various question types to ensure robust responses
- Document prompt changes and their rationale
