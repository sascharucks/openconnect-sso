# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

openconnect-sso is a Python wrapper script for OpenConnect that adds support for Azure AD (SAMLv2) authentication to Cisco SSL-VPNs. It provides a Qt6-based browser interface for handling the authentication flow.

## Development Commands

### Setup and Environment
- `make dev` - Initialize development environment (creates .venv/, installs dependencies via Poetry)
- `nix-shell` - Drop into Nix development environment (alternative to make dev)

### Testing and Quality Checks
- `make test` - Run all tests using pytest
- `make check` - Run all quality checks (pre-commit hooks + tests)
- `make pre-commit` - Run pre-commit hooks on all files
- `.venv/bin/pytest tests/test_browser.py` - Run specific test file
- `.venv/bin/pytest tests/test_browser.py::test_name` - Run specific test

### Build and Release
- `make dist` - Build distribution packages
- `make release` - Build release version in clean environment
- `make clean` - Remove temporary files and artifacts

## Code Architecture

### Entry Points
- **CLI** (`openconnect_sso/cli.py`): Command-line interface parsing and main entry point
- **App** (`openconnect_sso/app.py`): Main application orchestrator

### Core Components
1. **Authentication Flow**:
   - `authenticator.py`: Handles SAML/SSO authentication process
   - `saml_authenticator.py`: SAML-specific authentication logic
   - `browser/`: Qt6 WebEngine-based browser for authentication UI

2. **Configuration**:
   - `config.py`: Configuration management (stored in XDG config dir)
   - `profile.py`: VPN profile management
   - Config file: `$XDG_CONFIG_HOME/openconnect-sso/config.toml`

3. **Key Features**:
   - Credential storage via system keyring
   - TOTP (2FA) support
   - Proxy support
   - Custom openconnect arguments passthrough

### Authentication Flow
1. User runs `openconnect-sso` with server/profile info
2. App creates browser window for SAML authentication
3. User completes authentication in browser
4. App extracts session cookies/credentials
5. App launches openconnect with obtained credentials

## Testing Approach

- Framework: pytest with asyncio support
- Coverage tracking enabled
- Test files in `tests/` directory
- Run with `make test` or directly with pytest

## Code Style and Quality

- Formatter: Black (target Python 3.6-3.8)
- Linter: Flake8 with flake8-bugbear
- Pre-commit hooks configured for automatic checks
- Use `make check` before committing

## Important Notes

- Requires Python 3.8+
- Main dependencies: PyQt6, PyQt6-WebEngine, attrs, lxml, keyring, structlog
- External dependency: openconnect binary
- Uses Poetry for dependency management
- Supports both pip/pipx and Nix installation methods
- Configuration and credentials stored in user's home directory