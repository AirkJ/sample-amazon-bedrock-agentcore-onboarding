# AWS Cost Estimator Agent Flow Diagram

```mermaid
flowchart TD
    A[User Request: Architecture Description] --> B[AWSCostEstimatorAgent.__init__]
    
    B --> C[_estimation_agent Context Manager]
    
    C --> D[_setup_code_interpreter]
    D --> E[CodeInterpreter.start]
    E --> F{Code Interpreter Ready?}
    F -->|No| G[Log Error & Continue]
    F -->|Yes| H[_setup_aws_pricing_client]
    
    H --> I[_get_aws_credentials]
    I --> J[boto3.Session.get_credentials]
    J --> K[STS.get_caller_identity]
    K --> L[MCPClient Setup with Credentials]
    
    L --> M[aws_pricing_client.list_tools_sync]
    M --> N[Create Strands Agent with Tools]
    
    N --> O[Agent Processing: SYSTEM_PROMPT]
    
    O --> P[Step 1: Parse Architecture]
    P --> Q[Step 2: get_pricing_service_codes]
    Q --> R[Step 3: get_pricing_service_attributes]
    R --> S[Step 4: get_pricing_attribute_values]
    S --> T[Step 5: get_pricing with filters]
    
    T --> U[Step 6: execute_cost_calculation]
    U --> V[CodeInterpreter.invoke executeCode]
    V --> W[Secure Python Execution]
    W --> X[Extract Results from Stream]
    
    X --> Y[Generate Cost Report]
    Y --> Z[Return Formatted Response]
    
    Z --> AA[Context Manager Cleanup]
    AA --> BB[End]
    
    %% Error Handling
    G --> N
    
    %% Styling
    classDef initClass fill:#e1f5fe
    classDef setupClass fill:#f3e5f5
    classDef processClass fill:#e8f5e8
    classDef toolClass fill:#fff3e0
    classDef resultClass fill:#fce4ec
    
    class A,B initClass
    class C,D,E,H,I,J,K,L,M,N setupClass
    class O,P,Q,R,S,T processClass
    class U,V,W,X toolClass
    class Y,Z,AA,BB resultClass
```

## Key Components

### 🔧 **Initialization Phase**
- **AWSCostEstimatorAgent**: Main orchestrator
- **Region Setup**: Uses boto3 default or specified region
- **Logging Configuration**: Comprehensive error tracking

### 🛠️ **Setup Phase**
- **Code Interpreter**: Secure AgentCore sandbox initialization
- **AWS Credentials**: Retrieval with session token support
- **MCP Client**: AWS Pricing server with credential injection
- **Tool Integration**: Combines custom and MCP tools

### 🤖 **Processing Phase**
- **Architecture Parsing**: Identifies AWS services
- **Pricing Data Retrieval**: Sequential MCP tool calls
- **Secure Calculations**: AgentCore Code Interpreter execution
- **Result Formatting**: Structured cost report generation

### 🔄 **Resource Management**
- **Context Manager**: Ensures proper cleanup
- **Error Handling**: Graceful fallbacks throughout
- **Stream Processing**: Real-time response capability

## Tool Flow Sequence

1. `get_pricing_service_codes` → Available AWS services
2. `get_pricing_service_attributes` → Service-specific filters  
3. `get_pricing_attribute_values` → Valid attribute values
4. `get_pricing` → Actual pricing data with filters
5. `execute_cost_calculation` → Mathematical operations in sandbox

## Security Features

- **Sandboxed Execution**: All calculations in AgentCore
- **Credential Management**: Secure AWS credential handling
- **Resource Isolation**: Proper cleanup and error boundaries