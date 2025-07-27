# 🛠️ Contributing Guidelines

We welcome contributions to OpenHAIV 🤗

👇 If you're interested in improving the project, please follow these guidelines

## 🧹 Code Style

This project uses [pre-commit](https://pre-commit.com/) to automatically enforce code style and quality before each commit. Please install pre-commit and run:

```bash
pip install pre-commit
pre-commit install
```

The main checks include:

- flake8: PEP8 code style checking
- yapf: automatic Python code formatting
- codespell: spell checking
- docformatter: automatic docstring formatting
- trailing-whitespace, end-of-file-fixer, mixed-line-ending and other basic formatting fixes

See the `.pre-commit-config.yaml` file for detailed configuration. All these checks and fixes will be run automatically before every commit.

## 📜 Code of Conduct

Please note that all contributors are expected to follow our [Code of Conduct](https://github.com/HAIV-Lab/openhaiv/blob/main/CODE_OF_CONDUCT.md) to foster a welcoming and inclusive community.

## 🐛 Reporting Issues

1. **Check existing issues** first to avoid duplicates
2. **Use the issue template** when available
3. **Be specific** about the problem:
      - Include steps to reproduce
      - Provide environment details (OS, Python version, dependencies)
      - Add screenshots if applicable
      - Describe expected vs. actual behavior

## 💡 Submitting Pull Requests

1. **Create an issue first** to discuss major changes
2. **Fork the repository** and create a branch from `main`
3. **Follow the coding style** used throughout the project:
      - Adhere to PEP 8 guidelines
      - Use meaningful variable/function names
      - Add docstrings for new functions/classes
4. **Write tests** for new features
5. **Ensure all tests pass** before submitting
6. **Update documentation** reflecting your changes
7. **Make atomic commits** with clear messages