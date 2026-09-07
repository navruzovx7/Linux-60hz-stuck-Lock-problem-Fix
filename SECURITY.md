# Security Policy

## Supported Versions

This project is primarily a documentation and configuration project.

Security support is provided for the latest version of the repository.

| Version        | Supported |
| -------------- | --------- |
| Latest         | Yes       |
| Older releases | No        |

## Reporting a Vulnerability

If you discover a security vulnerability in this repository, please do not publicly disclose the details in an issue before the vulnerability has been reviewed.

Instead, use GitHub's private vulnerability reporting feature if it is enabled for this repository.

If private reporting is unavailable, contact the repository maintainer through the contact information provided on the GitHub repository profile.

### Please Include

When reporting a potential vulnerability, provide:

* A clear description of the issue
* Steps to reproduce it
* Affected files or components
* Potential impact
* Relevant logs or proof of concept, if safe to provide

Please avoid including passwords, authentication tokens, private keys, or other sensitive information.

## Scope

This repository mainly contains:

* Hyprland configuration examples
* Linux shell scripts
* Troubleshooting documentation
* Monitor configuration examples

Configuration files should not require privileged access unless explicitly documented.

Users should review scripts before executing them, especially scripts that use:

```bash
sudo
```

or modify files outside the user's home directory.

## Responsible Disclosure

Please allow reasonable time for the issue to be investigated before publicly disclosing sensitive vulnerability details.

Security reports will be reviewed as soon as reasonably possible.

Thank you for helping keep the project and its users safe.
