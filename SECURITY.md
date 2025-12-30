# Security Policy

## Supported Versions

We release patches for security vulnerabilities in the following versions:

| Version | Supported          |
| ------- | ------------------ |
| 1.0.x   | :white_check_mark: |
| < 1.0   | :x:                |

## Reporting a Vulnerability

We take the security of our research software seriously. If you discover a security vulnerability, please follow these steps:

### 1. DO NOT Disclose Publicly

Please do not disclose the vulnerability publicly until it has been addressed. This includes:
- GitHub issues
- Social media
- Public forums or mailing lists

### 2. Report Privately

Report security vulnerabilities by:

1. **Opening a private security advisory** on GitHub:
   - Go to the Security tab
   - Click "Report a vulnerability"
   - Fill out the advisory form

2. **Or via email** (if GitHub security advisories are unavailable):
   - Subject: "Security Vulnerability in Brest-Cancer Project"
   - Include detailed information (see below)

### 3. What to Include

Please provide the following information in your report:

```
Vulnerability Type: [e.g., Code Injection, Data Exposure, Authentication]

Affected Component: [e.g., feature_engineering.py, model_training.py]

Severity: [Critical / High / Medium / Low]

Description:
[Clear description of the vulnerability]

Steps to Reproduce:
1. [First step]
2. [Second step]
3. [...]

Impact:
[What could an attacker accomplish?]

Suggested Fix:
[If you have ideas for how to fix]

Environment:
- OS: [e.g., Ubuntu 20.04]
- Python Version: [e.g., 3.8.10]
- Package Versions: [relevant library versions]
```

### 4. What to Expect

- **Acknowledgment**: Within 48 hours of submission
- **Initial Assessment**: Within 5 business days
- **Status Updates**: Every 7 days until resolution
- **Resolution Timeline**: Varies by severity
  - Critical: 1-7 days
  - High: 7-30 days
  - Medium: 30-90 days
  - Low: 90+ days

## Security Best Practices for Users

### Data Privacy

1. **Never upload identifiable patient data** to this repository
2. **Use only de-identified, publicly available datasets**
3. **Comply with HIPAA, GDPR**, and other relevant regulations
4. **Do not commit sensitive data** (credentials, tokens, private keys)

### Environment Security

1. **Use virtual environments** to isolate dependencies
   ```bash
   python -m venv venv
   source venv/bin/activate
   ```

2. **Keep dependencies updated**
   ```bash
   pip install --upgrade -r requirements.txt
   ```

3. **Review dependency security** before installation
   ```bash
   pip install safety
   safety check
   ```

### Code Security

1. **Validate all inputs** before processing
2. **Use parameterized queries** for any database operations
3. **Sanitize file paths** to prevent directory traversal
4. **Limit resource usage** to prevent DoS
5. **Handle exceptions properly** without exposing sensitive information

### Credential Management

1. **Never hardcode credentials** in source code
2. **Use environment variables** for sensitive configuration
   ```python
   import os
   api_key = os.environ.get('API_KEY')
   ```

3. **Use .env files** (excluded from git) for local development
4. **Rotate credentials** regularly

## Known Security Considerations

### Data Handling

- **Risk**: Accidental exposure of patient data
- **Mitigation**: Only use publicly available, de-identified datasets from GEO and TCGA

### Dependency Vulnerabilities

- **Risk**: Vulnerabilities in third-party packages
- **Mitigation**: Regular dependency updates, security scanning

### Model Adversarial Attacks

- **Risk**: Malicious inputs designed to manipulate predictions
- **Mitigation**: Input validation, model robustness testing (future enhancement)

### Code Injection

- **Risk**: Unsafe eval() or exec() usage, command injection
- **Mitigation**: No dynamic code execution, parameterized commands

## Secure Development Guidelines

### For Contributors

1. **Code Review**: All PRs require review before merging
2. **Static Analysis**: Use tools like `bandit` for security scanning
   ```bash
   pip install bandit
   bandit -r .
   ```

3. **Dependency Scanning**: Check for known vulnerabilities
   ```bash
   pip install pip-audit
   pip-audit
   ```

4. **Secrets Scanning**: Ensure no secrets in commits
   ```bash
   # Use tools like git-secrets or gitleaks
   git-secrets --scan
   ```

### Security Checklist for PRs

Before submitting a PR, ensure:

- [ ] No hardcoded secrets or credentials
- [ ] Input validation for all user-provided data
- [ ] Proper error handling without information leakage
- [ ] Dependencies are up to date and scanned
- [ ] No SQL injection vulnerabilities
- [ ] File operations use safe paths
- [ ] No unsafe deserialization (pickle with untrusted data)
- [ ] Logging doesn't expose sensitive information

## Vulnerability Disclosure Policy

### Our Commitment

- We will acknowledge your report within 48 hours
- We will keep you informed of our progress
- We will credit you in the security advisory (unless you prefer anonymity)

### Coordinated Disclosure

We follow coordinated disclosure:

1. **Private Fix**: We develop and test a fix privately
2. **Security Advisory**: We prepare a security advisory
3. **Release**: We release the patched version
4. **Public Disclosure**: We publish the advisory after users have time to update

### Timeline

- **Critical vulnerabilities**: Disclosed 7 days after patch release
- **High vulnerabilities**: Disclosed 30 days after patch release
- **Medium/Low vulnerabilities**: Disclosed 90 days after patch release

## Security Updates

Security updates are announced via:

1. **GitHub Security Advisories**: Primary notification channel
2. **Release Notes**: Detailed in CHANGELOG.md
3. **README**: Critical updates highlighted

## Scope

### In Scope

- Security vulnerabilities in project code
- Dependency vulnerabilities affecting the project
- Data privacy or exposure issues
- Authentication or authorization flaws (if implemented)
- Code injection or execution vulnerabilities

### Out of Scope

- Issues in external dependencies (report to upstream maintainers)
- Social engineering attacks
- Physical security issues
- Issues requiring physical access to machines
- Issues in unmaintained/deprecated versions

## Bug Bounty

We currently do not offer a bug bounty program as this is a research/educational project. However, we deeply appreciate security researchers who help improve the project's security.

## Recognition

Security researchers who report valid vulnerabilities will be:

- Credited in the security advisory (with their permission)
- Listed in a Security Hall of Fame (coming soon)
- Acknowledged in release notes

## Compliance

This project aims to follow security best practices for:

- **OWASP Top 10**: Web application security risks
- **CWE Top 25**: Most dangerous software weaknesses
- **Python Security Best Practices**: Per OWASP Python Security Project

## Questions?

For questions about this security policy, please open a public issue (without disclosing vulnerabilities) or contact the maintainers.

---

**Remember**: This is a research tool and should NOT be used for clinical decision-making without proper validation and regulatory approval.

Thank you for helping keep this project and its users safe! 🔒
