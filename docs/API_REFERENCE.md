# MCP Automation System - API Reference

## 📚 Complete API Documentation

This document provides comprehensive API reference for the MCP Automation System components.

---

## 🏗️ System Architecture APIs

### Core System API

#### `MCPSystem` Class

Main system controller for the MCP automation framework.

```python
class MCPSystem:
    """Core MCP automation system controller."""
    
    def __init__(self, config_path: str = None):
        """Initialize MCP system.
        
        Args:
            config_path: Path to configuration file
        """
        
    async def start(self):
        """Start the MCP system.
        
        Returns:
            Dict[str, Any]: System startup status
            
        Raises:
            MCPSystemError: If system fails to start
        """
        
    async def stop(self):
        """Stop the MCP system gracefully.
        
        Returns:
            Dict[str, Any]: System shutdown status
        """
        
    async def health_check(self):
        """Perform comprehensive health check.
        
        Returns:
            Dict[str, Any]: {
                "status": "healthy|degraded|unhealthy",
                "checks": {
                    "database": "OK|ERROR",
                    "network": "OK|ERROR",
                    "services": "OK|ERROR"
                },
                "timestamp": float
            }
        """
```

---

## 🔐 Privacy Scanner API

### `PrivacyScanner` Class

Advanced privacy scanning and remediation system.

```python
class PrivacyScanner:
    """Enterprise privacy scanner for blockchain AI infrastructure."""
    
    def __init__(self, config_path: str = None):
        """Initialize privacy scanner.
        
        Args:
            config_path: Path to scanner configuration
        """
        
    def scan_file(self, file_path: str) -> List[ScanResult]:
        """Scan a single file for privacy violations.
        
        Args:
            file_path: Path to file to scan
            
        Returns:
            List[ScanResult]: List of privacy scan findings
            
        Example:
            >>> scanner = PrivacyScanner()
            >>> results = scanner.scan_file("config.py")
            >>> for result in results:
            ...     print(f"Found {result.severity}: {result.description}")
        """
        
    def scan_directory(self, directory: str) -> List[ScanResult]:
        """Scan all files in a directory.
        
        Args:
            directory: Path to directory to scan
            
        Returns:
            List[ScanResult]: Complete scan results
        """
        
    def generate_report(self, output_format: str = "text") -> str:
        """Generate formatted scan report.
        
        Args:
            output_format: Format for report ("text", "json", "yaml")
            
        Returns:
            str: Formatted report
            
        Example:
            >>> scanner = PrivacyScanner()
            >>> scanner.scan_directory(".")
            >>> report = scanner.generate_report("json")
            >>> print(report)
        """
```

### `ScanResult` DataClass

```python
@dataclass
class ScanResult:
    """Privacy scan finding result."""
    
    file_path: str          # Path to file with issue
    line_number: int        # Line number of issue
    line_content: str       # Content of problematic line
    pattern_name: str       # Name of matched pattern
    severity: str           # Severity level (critical|high|medium|low)
    description: str        # Human-readable description
    recommendation: str     # Recommended remediation action
```

---

## 🚀 Deployment API

### `DeploymentManager` Class

```python
class DeploymentManager:
    """Manage system deployments and rollbacks."""
    
    def __init__(self, config: Dict[str, Any]):
        """Initialize deployment manager.
        
        Args:
            config: Deployment configuration
        """
        
    async def deploy(self, 
                    environment: str,
                    version: str = None,
                    dry_run: bool = False) -> DeploymentResult:
        """Deploy system to specified environment.
        
        Args:
            environment: Target environment (development|staging|production)
            version: Version to deploy (defaults to current)
            dry_run: If True, validate deployment without executing
            
        Returns:
            DeploymentResult: Deployment status and details
            
        Raises:
            DeploymentError: If deployment fails
            
        Example:
            >>> manager = DeploymentManager(config)
            >>> result = await manager.deploy("staging", dry_run=True)
            >>> if result.success:
            ...     await manager.deploy("staging")
        """
        
    async def rollback(self, 
                      environment: str,
                      target_version: str = None) -> RollbackResult:
        """Rollback deployment to previous version.
        
        Args:
            environment: Target environment
            target_version: Specific version to rollback to
            
        Returns:
            RollbackResult: Rollback status and details
        """
        
    async def get_deployment_status(self, environment: str) -> DeploymentStatus:
        """Get current deployment status.
        
        Args:
            environment: Environment to check
            
        Returns:
            DeploymentStatus: Current deployment information
        """
```

### Deployment Data Structures

```python
@dataclass
class DeploymentResult:
    """Result of deployment operation."""
    
    success: bool
    environment: str
    version: str
    deployment_id: str
    start_time: datetime
    end_time: datetime
    duration_seconds: float
    checks_passed: List[str]
    checks_failed: List[str]
    rollback_available: bool
    message: str
    
@dataclass 
class DeploymentStatus:
    """Current deployment status."""
    
    environment: str
    current_version: str
    deployment_id: str
    deployed_at: datetime
    health_status: str
    services_running: List[str]
    services_failed: List[str]
    last_health_check: datetime
```

---

## 📊 Monitoring API

### `MonitoringManager` Class

```python
class MonitoringManager:
    """System monitoring and alerting management."""
    
    def __init__(self, config: Dict[str, Any]):
        """Initialize monitoring manager.
        
        Args:
            config: Monitoring configuration
        """
        
    async def get_system_metrics(self) -> SystemMetrics:
        """Get current system metrics.
        
        Returns:
            SystemMetrics: Current system performance data
            
        Example:
            >>> monitor = MonitoringManager(config)
            >>> metrics = await monitor.get_system_metrics()
            >>> print(f"CPU: {metrics.cpu_percent}%")
            >>> print(f"Memory: {metrics.memory_percent}%")
        """
        
    async def start_monitoring(self, interval: int = 300):
        """Start continuous monitoring.
        
        Args:
            interval: Monitoring interval in seconds
        """
        
    async def stop_monitoring(self):
        """Stop continuous monitoring."""
        
    async def create_alert(self, alert: AlertConfig):
        """Create new monitoring alert.
        
        Args:
            alert: Alert configuration
        """
        
    async def get_alerts(self, 
                        severity: str = None,
                        active_only: bool = True) -> List[Alert]:
        """Get monitoring alerts.
        
        Args:
            severity: Filter by severity level
            active_only: Return only active alerts
            
        Returns:
            List[Alert]: Matching alerts
        """
```

### Monitoring Data Structures

```python
@dataclass
class SystemMetrics:
    """System performance metrics."""
    
    timestamp: datetime
    cpu_percent: float
    memory_percent: float
    disk_percent: float
    network_io: Dict[str, int]  # bytes_sent, bytes_recv
    disk_io: Dict[str, int]     # read_bytes, write_bytes
    process_count: int
    load_average: Tuple[float, float, float]  # 1min, 5min, 15min
    
@dataclass
class Alert:
    """Monitoring alert."""
    
    id: str
    name: str
    severity: str
    description: str
    condition: str
    threshold: float
    current_value: float
    triggered_at: datetime
    resolved_at: Optional[datetime]
    active: bool
    notifications_sent: int
```

---

## 🤖 AI Agent API

### `AIAgentManager` Class

```python
class AIAgentManager:
    """Manage AI agents for blockchain operations."""
    
    def __init__(self, config: Dict[str, Any]):
        """Initialize AI agent manager.
        
        Args:
            config: AI agent configuration
        """
        
    async def create_agent(self, agent_config: AgentConfig) -> Agent:
        """Create new AI agent.
        
        Args:
            agent_config: Agent configuration
            
        Returns:
            Agent: Created agent instance
            
        Example:
            >>> manager = AIAgentManager(config)
            >>> agent_config = AgentConfig(
            ...     name="trading_agent",
            ...     type="trading",
            ...     blockchain="ethereum"
            ... )
            >>> agent = await manager.create_agent(agent_config)
        """
        
    async def start_agent(self, agent_id: str):
        """Start AI agent.
        
        Args:
            agent_id: Agent identifier
        """
        
    async def stop_agent(self, agent_id: str):
        """Stop AI agent.
        
        Args:
            agent_id: Agent identifier
        """
        
    async def get_agent_status(self, agent_id: str) -> AgentStatus:
        """Get agent status.
        
        Args:
            agent_id: Agent identifier
            
        Returns:
            AgentStatus: Current agent status
        """
        
    async def list_agents(self, 
                         status: str = None,
                         agent_type: str = None) -> List[Agent]:
        """List agents with optional filtering.
        
        Args:
            status: Filter by status (running|stopped|error)
            agent_type: Filter by agent type
            
        Returns:
            List[Agent]: Matching agents
        """
```

### AI Agent Data Structures

```python
@dataclass
class AgentConfig:
    """AI agent configuration."""
    
    name: str
    agent_type: str
    blockchain: str
    model: str
    parameters: Dict[str, Any]
    resources: ResourceConfig
    scheduling: ScheduleConfig
    
@dataclass
class Agent:
    """AI agent instance."""
    
    id: str
    name: str
    agent_type: str
    status: str
    created_at: datetime
    last_activity: datetime
    performance_metrics: Dict[str, float]
    configuration: AgentConfig
    
@dataclass
class AgentStatus:
    """AI agent status information."""
    
    agent_id: str
    status: str  # running|stopped|error|maintenance
    health_score: float
    last_execution: datetime
    executions_today: int
    success_rate: float
    error_count: int
    resource_usage: Dict[str, float]
    next_scheduled_run: Optional[datetime]
```

---

## 🔗 Blockchain Integration API

### `BlockchainManager` Class

```python
class BlockchainManager:
    """Manage blockchain connections and operations."""
    
    def __init__(self, config: Dict[str, Any]):
        """Initialize blockchain manager.
        
        Args:
            config: Blockchain configuration
        """
        
    async def get_connection(self, network: str) -> BlockchainConnection:
        """Get blockchain network connection.
        
        Args:
            network: Network name (ethereum|polygon|arbitrum|optimism)
            
        Returns:
            BlockchainConnection: Network connection instance
            
        Raises:
            NetworkError: If connection fails
            
        Example:
            >>> manager = BlockchainManager(config)
            >>> eth_conn = await manager.get_connection("ethereum")
            >>> block = await eth_conn.get_latest_block()
        """
        
    async def get_block(self, 
                       network: str,
                       block_number: int = None) -> Block:
        """Get block information.
        
        Args:
            network: Blockchain network
            block_number: Block number (latest if None)
            
        Returns:
            Block: Block information
        """
        
    async def send_transaction(self,
                              network: str,
                              transaction: Transaction) -> TransactionResult:
        """Send blockchain transaction.
        
        Args:
            network: Target network
            transaction: Transaction to send
            
        Returns:
            TransactionResult: Transaction execution result
        """
        
    async def get_logs(self,
                      network: str,
                      filter_params: LogFilter) -> List[Log]:
        """Query blockchain logs.
        
        Args:
            network: Blockchain network
            filter_params: Log filter parameters
            
        Returns:
            List[Log]: Matching log entries
        """
```

---

## ⚙️ Configuration API

### `ConfigManager` Class

```python
class ConfigManager:
    """Manage system configuration."""
    
    def __init__(self, config_path: str):
        """Initialize configuration manager.
        
        Args:
            config_path: Path to main configuration file
        """
        
    def get(self, key: str, default: Any = None) -> Any:
        """Get configuration value.
        
        Args:
            key: Configuration key (supports dot notation)
            default: Default value if key not found
            
        Returns:
            Any: Configuration value
            
        Example:
            >>> config = ConfigManager("config/mcp-config.yaml")
            >>> db_host = config.get("database.host", "localhost")
            >>> api_port = config.get("api.port", 8000)
        """
        
    def set(self, key: str, value: Any):
        """Set configuration value.
        
        Args:
            key: Configuration key
            value: Configuration value
        """
        
    def reload(self):
        """Reload configuration from file."""
        
    def validate(self) -> ValidationResult:
        """Validate current configuration.
        
        Returns:
            ValidationResult: Validation status and issues
        """
        
    def get_environment_config(self, env: str) -> Dict[str, Any]:
        """Get environment-specific configuration.
        
        Args:
            env: Environment name
            
        Returns:
            Dict[str, Any]: Environment configuration
        """
```

---

## 🚨 Error Handling

### Exception Classes

```python
class MCPError(Exception):
    """Base exception for MCP system errors."""
    pass
    
class MCPSystemError(MCPError):
    """System-level errors."""
    pass
    
class DeploymentError(MCPError):
    """Deployment-related errors."""
    pass
    
class ConfigurationError(MCPError):
    """Configuration-related errors."""
    pass
    
class NetworkError(MCPError):
    """Network and connectivity errors."""
    pass
    
class SecurityError(MCPError):
    """Security and privacy errors."""
    pass
    
class ValidationError(MCPError):
    """Data validation errors."""
    pass
```

### Error Response Format

```python
@dataclass
class ErrorResponse:
    """Standard error response format."""
    
    error_code: str
    message: str
    details: Dict[str, Any]
    timestamp: datetime
    request_id: str
    suggestions: List[str]
    
    def to_dict(self) -> Dict[str, Any]:
        """Convert to dictionary format."""
        return {
            "error": {
                "code": self.error_code,
                "message": self.message,
                "details": self.details,
                "timestamp": self.timestamp.isoformat(),
                "request_id": self.request_id,
                "suggestions": self.suggestions
            }
        }
```

---

## 📝 Usage Examples

### Basic System Setup

```python
import asyncio
from mcp_automation import MCPSystem, ConfigManager

async def main():
    # Initialize configuration
    config = ConfigManager("config/mcp-config.yaml")
    
    # Create and start system
    system = MCPSystem(config)
    await system.start()
    
    # Run health check
    health = await system.health_check()
    print(f"System status: {health['status']}")
    
    # Clean shutdown
    await system.stop()

asyncio.run(main())
```

### Privacy Scanning

```python
from mcp_automation.privacy import PrivacyScanner

def scan_project():
    scanner = PrivacyScanner()
    results = scanner.scan_directory(".")
    
    critical_issues = [r for r in results if r.severity == "critical"]
    if critical_issues:
        print(f"Found {len(critical_issues)} critical privacy issues!")
        for issue in critical_issues:
            print(f"  {issue.file_path}:{issue.line_number} - {issue.description}")
    
    # Generate report
    report = scanner.generate_report("json")
    with open("privacy-report.json", "w") as f:
        f.write(report)

scan_project()
```

### AI Agent Management

```python
from mcp_automation.agents import AIAgentManager, AgentConfig

async def deploy_trading_agent():
    manager = AIAgentManager(config)
    
    # Create agent configuration
    agent_config = AgentConfig(
        name="ethereum_trader",
        agent_type="trading",
        blockchain="ethereum",
        model="gpt-4",
        parameters={
            "risk_tolerance": 0.05,
            "max_position_size": 1000
        }
    )
    
    # Deploy and start agent
    agent = await manager.create_agent(agent_config)
    await manager.start_agent(agent.id)
    
    # Monitor status
    status = await manager.get_agent_status(agent.id)
    print(f"Agent {agent.name} status: {status.status}")

asyncio.run(deploy_trading_agent())
```

### Blockchain Operations

```python
from mcp_automation.blockchain import BlockchainManager

async def query_blockchain():
    manager = BlockchainManager(config)
    
    # Get latest block
    connection = await manager.get_connection("ethereum")
    latest_block = await manager.get_block("ethereum")
    print(f"Latest block: {latest_block.number}")
    
    # Query logs
    filter_params = LogFilter(
        from_block=latest_block.number - 100,
        to_block=latest_block.number,
        address="0x1234567890123456789012345678901234567890"
    )
    
    logs = await manager.get_logs("ethereum", filter_params)
    print(f"Found {len(logs)} log entries")

asyncio.run(query_blockchain())
```

---

## 🔄 API Versioning

The MCP Automation API follows semantic versioning:

- **Major version**: Breaking changes
- **Minor version**: New features, backward compatible
- **Patch version**: Bug fixes, backward compatible

### Current Version: 1.0.0

### Version Compatibility

- **1.0.x**: Full compatibility
- **1.x.x**: Backward compatible (deprecated features marked)
- **2.x.x**: Breaking changes (migration guide provided)

### API Headers

All API requests should include:

```http
API-Version: 1.0
Content-Type: application/json
Authorization: Bearer <token>
```

---

## 📞 Support and Documentation

- **GitHub Repository**: [blockchain-ai-infrastructure](https://github.com/protechtimenow/blockchain-ai-infrastructure)
- **Issues**: Report bugs and request features
- **Discussions**: Community support and questions
- **Documentation**: Complete guides and tutorials

---

*This API reference is automatically generated from code documentation and kept up-to-date with each release.*