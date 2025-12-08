# Security Policy

## Supported Versions

| Repository | Version | Supported |
| --- | --- | --- |
| bioquery-api | latest | :white_check_mark: |
| bioquery-frontend | latest | :white_check_mark: |
| bioquery-py | >= 0.1.0 | :white_check_mark: |
| bioquery-docs | latest | :white_check_mark: |

## Reporting a Vulnerability

We take security vulnerabilities seriously. If you discover a security issue, please report it responsibly.

### How to Report

1. **Do NOT** create a public GitHub issue for security vulnerabilities
2. Email security concerns to: **security@bioquery.io**
3. Include as much detail as possible:
   - Description of the vulnerability
   - Steps to reproduce
   - Potential impact
   - Suggested fix (if any)

### What to Expect

- **Acknowledgment**: We will acknowledge receipt within 48 hours
- **Assessment**: We will assess the vulnerability and determine its severity
- **Updates**: We will keep you informed of our progress
- **Resolution**: We aim to resolve critical vulnerabilities within 7 days
- **Credit**: We will credit you in the release notes (if desired)

### Scope

This security policy applies to:
- BioQuery API (https://api.bioquery.io)
- BioQuery Web App (https://bioquery.io)
- BioQuery Python SDK (pip install bioquery)
- BioQuery Documentation (https://docs.bioquery.io)

### Out of Scope

- Vulnerabilities in third-party dependencies (report these to the respective maintainers)
- Social engineering attacks
- Physical attacks
- Denial of service attacks

## Security Best Practices

When contributing to BioQuery:

1. **Never commit secrets** - Use environment variables for API keys and credentials
2. **Validate inputs** - Always validate user inputs on both client and server
3. **Use parameterized queries** - Prevent SQL injection in BigQuery operations
4. **Keep dependencies updated** - Regularly update dependencies to patch known vulnerabilities
5. **Follow least privilege** - Request only necessary permissions

## Contact

For security questions or concerns:
- Email: security@bioquery.io
- GitHub Security Advisories: [Create a private advisory](https://github.com/BioQuery-io/.github/security/advisories/new)
