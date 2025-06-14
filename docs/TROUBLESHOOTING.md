# MCP Automation System - Troubleshooting Guide

## 🔍 Common Issues and Solutions

This guide helps you diagnose and resolve common issues with the MCP Automation System.

## 📋 Quick Diagnostic Commands

### System Health Check
```bash
# Run comprehensive health check
./mcp-automation/scripts/deploy.sh --health-check

# Check system status
./scripts/monitor.sh --status

# Run privacy scan
python3 mcp-automation/scripts/privacy-scan.py --scan-all --report
```

### Log Analysis
```bash
# View application logs
tail -f logs/mcp-automation.log

# Check for errors
grep "ERROR" logs/mcp-automation.log | tail -20

# Monitor deployment logs
tail -f logs/deployment.log
```

---

## 🚨 Installation Issues

### Problem: "Permission denied" when running scripts
**Symptoms:**
- Scripts fail with permission errors
- Cannot execute `.sh` files

**Solution:**
```bash
# Fix script permissions
chmod +x mcp-automation/scripts/*.sh
chmod +x scripts/*.sh

# Fix directory permissions
chmod 755 logs/
chmod 755 data/
chmod 755 mcp-automation/
```

### Problem: Python dependencies not installing
**Symptoms:**
- `pip install` failures
- Import errors for required packages

**Solution:**
```bash
# Update pip
python -m pip install --upgrade pip

# Install with verbose output
pip install -r requirements.txt -v

# Try with user install if permission issues
pip install -r requirements.txt --user

# Clear cache and retry
pip cache purge
pip install -r requirements.txt
```

### Problem: Virtual environment issues
**Symptoms:**
- Cannot activate virtual environment
- Wrong Python version being used

**Solution:**
```bash
# Remove existing venv
rm -rf venv/

# Create new virtual environment
python3 -m venv venv

# Activate (Linux/macOS)
source venv/bin/activate

# Activate (Windows)
venv\Scripts\activate

# Verify Python version
python --version
which python
```

---

## ⚙️ Configuration Issues

### Problem: Configuration file not found
**Symptoms:**
- "Configuration file not found" errors
- System using default values

**Solution:**
```bash
# Copy template
cp mcp-automation/config/secrets.env.template mcp-automation/config/secrets.env

# Verify file exists
ls -la mcp-automation/config/

# Check file permissions
chmod 600 mcp-automation/config/secrets.env
```

### Problem: Invalid YAML configuration
**Symptoms:**
- YAML parsing errors
- "Invalid configuration" messages

**Solution:**
```bash
# Validate YAML syntax
python3 -c "import yaml; yaml.safe_load(open('mcp-automation/config/mcp-config.yaml'))"

# Check for common YAML issues
# - Consistent indentation (use spaces, not tabs)
# - Proper quoting of strings with special characters
# - No trailing spaces

# Fix common issues
sed -i 's/\t/  /g' mcp-automation/config/mcp-config.yaml  # Replace tabs with spaces
```

### Problem: Environment variables not loading
**Symptoms:**
- Default values being used
- "Environment variable not set" warnings

**Solution:**
```bash
# Check environment file
cat mcp-automation/config/secrets.env

# Load environment manually
source mcp-automation/config/secrets.env  # If using source
export $(cat mcp-automation/config/secrets.env | xargs)  # Alternative method

# Verify variables are set
echo $ENVIRONMENT
echo $DATABASE_URL

# Check for syntax errors in env file
# Remove spaces around = signs
# Ensure no special characters without quotes
```

---

## 🗄️ Database Issues

### Problem: Database connection failed
**Symptoms:**
- "Cannot connect to database" errors
- Application startup failures

**Solution:**
```bash
# Check database file (SQLite)
ls -la data/mcp_automation.db

# Test database connectivity
sqlite3 data/mcp_automation.db ".tables"

# Reset database if corrupted
rm data/mcp_automation.db
./scripts/setup-database.sh

# For PostgreSQL/MySQL, check connection
psql -h $DATABASE_HOST -U $DATABASE_USER -d $DATABASE_NAME -c "\l"
```

### Problem: Database permissions
**Symptoms:**
- "Permission denied" on database operations
- Cannot create/update records

**Solution:**
```bash
# Fix database file permissions
chmod 664 data/mcp_automation.db
chown $USER:$USER data/mcp_automation.db

# Ensure directory permissions
chmod 755 data/

# For PostgreSQL, check user permissions
psql -c "\du" # List users and permissions
```

---

## 🌐 Network and API Issues

### Problem: Blockchain RPC connection failed
**Symptoms:**
- "Network unreachable" errors
- Timeout errors for blockchain calls

**Solution:**
```bash
# Test RPC connectivity
curl -X POST -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' \
  $ETHEREUM_RPC_URL

# Check API key validity
echo $ETHEREUM_RPC_URL | grep -o 'your_api_key' && echo "API key not set!"

# Test with different RPC provider
export ETHEREUM_RPC_URL="https://cloudflare-eth.com"

# Check rate limits
curl -I $ETHEREUM_RPC_URL
```

### Problem: Internet connectivity issues
**Symptoms:**
- DNS resolution failures
- Cannot reach external APIs

**Solution:**
```bash
# Test basic connectivity
ping -c 3 8.8.8.8

# Test DNS resolution
nslookup google.com

# Test HTTPS connectivity
curl -I https://google.com

# Check system DNS configuration
cat /etc/resolv.conf

# Try alternative DNS servers
echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.conf
```

### Problem: API rate limiting
**Symptoms:**
- "Too Many Requests" errors
- 429 HTTP status codes

**Solution:**
```bash
# Check current rate limit status
curl -I $API_ENDPOINT | grep -i "rate-limit"

# Implement exponential backoff in scripts
# Add delays between API calls
sleep 1  # Add to scripts between API calls

# Use multiple API keys for higher limits
# Configure backup RPC URLs
```

---

## 🔒 Security and Privacy Issues

### Problem: Privacy scan failing
**Symptoms:**
- Privacy scanner exits with errors
- Cannot complete security validation

**Solution:**
```bash
# Run privacy scan with verbose output
python3 mcp-automation/scripts/privacy-scan.py --scan-all --verbose

# Check for missing dependencies
pip install pyyaml requests

# Test with single file first
python3 mcp-automation/scripts/privacy-scan.py README.md

# Update privacy patterns
cp mcp-automation/config/privacy-patterns.yaml.template mcp-automation/config/privacy-patterns.yaml
```

### Problem: Secrets detected in code
**Symptoms:**
- CI/CD failing on security checks
- Privacy scanner finding sensitive data

**Solution:**
```bash
# Remove secrets from git history
git filter-branch --force --index-filter \
  'git rm --cached --ignore-unmatch path/to/secret/file' \
  --prune-empty --tag-name-filter cat -- --all

# Add to .gitignore
echo "secrets.env" >> .gitignore
echo "*.key" >> .gitignore
echo "*.pem" >> .gitignore

# Use environment variables instead
export SECRET_VALUE="actual_secret"
echo 'SECRET_VALUE=${SECRET_VALUE:-placeholder}' > config/secrets.env.template
```

### Problem: SSL/TLS certificate errors
**Symptoms:**
- "Certificate verification failed" errors
- HTTPS connection issues

**Solution:**
```bash
# Update system certificates
sudo apt-get update && sudo apt-get install ca-certificates  # Ubuntu/Debian
brew install ca-certificates  # macOS

# Test SSL connection
openssl s_client -connect api.example.com:443 -servername api.example.com

# Temporarily disable SSL verification (not recommended for production)
export PYTHONHTTPSVERIFY=0
export REQUESTS_CA_BUNDLE=""
```

---

## 🚀 Deployment Issues

### Problem: Deployment script failing
**Symptoms:**
- Deployment exits with error codes
- Services not starting properly

**Solution:**
```bash
# Run deployment with verbose output
./mcp-automation/scripts/deploy.sh --env development --validate

# Check deployment logs
tail -f logs/deployment.log

# Run individual deployment steps
./mcp-automation/scripts/deploy.sh --setup
./mcp-automation/scripts/deploy.sh --health-check

# Reset and retry
./mcp-automation/scripts/deploy.sh --clean
./mcp-automation/scripts/deploy.sh --env development
```

### Problem: Service startup failures
**Symptoms:**
- Applications not responding
- Process exits immediately

**Solution:**
```bash
# Check process status
ps aux | grep mcp

# Check for port conflicts
sudo netstat -tulpn | grep :8000

# Start with debug mode
DEBUG=true python3 app.py

# Check service logs
sudo journalctl -u mcp-automation -f

# Verify dependencies
ldd $(which python3)
python3 -c "import sys; print(sys.path)"
```

### Problem: Docker deployment issues
**Symptoms:**
- Docker build failures
- Container exits immediately

**Solution:**
```bash
# Check Docker status
docker --version
sudo systemctl status docker

# Build with verbose output
docker build -t mcp-automation . --progress=plain

# Check build logs
docker build -t mcp-automation . 2>&1 | tee docker-build.log

# Run container interactively
docker run -it --entrypoint /bin/bash mcp-automation

# Check container logs
docker logs container_name

# Clean up and rebuild
docker system prune
docker build --no-cache -t mcp-automation .
```

---

## 📊 Performance Issues

### Problem: Slow response times
**Symptoms:**
- API calls taking too long
- System feels sluggish

**Solution:**
```bash
# Monitor system resources
top -p $(pgrep -f mcp-automation)

# Check disk I/O
iostat -x 1 5

# Monitor network traffic
iftop

# Check for memory leaks
valgrind --tool=memcheck python3 app.py

# Profile Python code
python3 -m cProfile -o profile.stats app.py
```

### Problem: High memory usage
**Symptoms:**
- Out of memory errors
- System becomes unresponsive

**Solution:**
```bash
# Check memory usage
free -h
ps aux --sort=-%mem | head

# Monitor memory over time
watch -n 5 'free -h'

# Check for memory leaks
python3 -c "import tracemalloc; tracemalloc.start(); # your code here"

# Increase swap space if needed
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

### Problem: Disk space issues
**Symptoms:**
- "No space left on device" errors
- Log files growing too large

**Solution:**
```bash
# Check disk usage
df -h
du -sh logs/

# Clean up logs
find logs/ -name "*.log" -mtime +7 -delete
logrotate /etc/logrotate.conf

# Clean up temporary files
sudo apt-get autoclean
sudo apt-get autoremove
rm -rf /tmp/*

# Set up log rotation
echo '/var/log/mcp-automation/*.log {
    daily
    rotate 7
    compress
    delaycompress
    missingok
    notifempty
}' | sudo tee /etc/logrotate.d/mcp-automation
```

---

## 🛠️ Development Issues

### Problem: Code formatting/linting failures
**Symptoms:**
- CI/CD failing on code quality checks
- Pre-commit hooks failing

**Solution:**
```bash
# Install development tools
pip install black flake8 mypy pre-commit

# Format code
black .

# Fix import sorting
isort .

# Run linting
flake8 . --max-line-length=127

# Type checking
mypy . --ignore-missing-imports

# Set up pre-commit hooks
pre-commit install
pre-commit run --all-files
```

### Problem: Test failures
**Symptoms:**
- pytest exits with failures
- Tests not running at all

**Solution:**
```bash
# Install test dependencies
pip install pytest pytest-cov pytest-asyncio

# Run tests with verbose output
python -m pytest tests/ -v

# Run specific test
python -m pytest tests/test_system.py::TestMCPSystem::test_health_check -v

# Run with coverage
python -m pytest tests/ --cov=. --cov-report=html

# Debug test failures
python -m pytest tests/ --pdb  # Drop into debugger on failure
```

---

## 📞 Getting Additional Help

### Diagnostic Information to Collect

When seeking help, please provide:

```bash
# System information
uname -a
python3 --version
pip --version

# Configuration status
ls -la mcp-automation/config/
head -20 logs/mcp-automation.log

# Environment information
env | grep -E "(ENVIRONMENT|DEBUG|DATABASE|API)"

# Error logs
grep "ERROR" logs/*.log | tail -50

# System resources
free -h
df -h
ps aux | grep mcp
```

### Support Channels

1. **GitHub Issues**: [Create an issue](https://github.com/protechtimenow/blockchain-ai-infrastructure/issues)
2. **Documentation**: Check the [complete documentation](../docs/)
3. **Community Discussion**: GitHub Discussions
4. **Enterprise Support**: Contact maintainers directly

### Before Contacting Support

- [ ] Checked this troubleshooting guide
- [ ] Reviewed recent log files
- [ ] Tried basic diagnostic commands
- [ ] Verified configuration files
- [ ] Tested with minimal configuration
- [ ] Collected diagnostic information

---

## 🔧 Prevention Tips

### Regular Maintenance

```bash
# Daily health checks
./scripts/monitor.sh --check

# Weekly log cleanup
find logs/ -name "*.log" -mtime +7 -delete

# Monthly security scans
python3 mcp-automation/scripts/privacy-scan.py --scan-all --report

# Quarterly dependency updates
pip list --outdated
pip install -r requirements.txt --upgrade
```

### Monitoring Setup

```bash
# Set up continuous monitoring
./scripts/monitor.sh --start 300  # Check every 5 minutes

# Configure alerting
echo "ALERT_EMAIL=your-email@example.com" >> mcp-automation/config/secrets.env

# Enable verbose logging during troubleshooting
echo "LOG_LEVEL=DEBUG" >> mcp-automation/config/secrets.env
```

---

**Remember**: Most issues can be resolved by checking logs, verifying configuration, and ensuring proper permissions. When in doubt, start with the basic diagnostic commands and work your way through the specific issue categories.