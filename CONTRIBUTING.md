# Contributing to Qt VS Code Windows Setup

Thank you for your interest in improving this documentation!

## How to Contribute

1. Fork this repository
2. Create a branch for your changes: `git checkout -b improve-documentation`
3. Make your improvements
4. Test any commands or procedures you document
5. Commit your changes: `git commit -m "Brief description of changes"`
6. Push to your fork: `git push origin improve-documentation`
7. Submit a Pull Request with a clear description

## Guidelines

- **Keep it lean** - Documentation should be concise and focused
- **Test everything** - Verify all commands and procedures work on Windows
- **Be consistent** - Follow existing markdown formatting and style
- **Update related sections** - If changing one document, check if others need updates
- **Use proper links** - Cross-reference between documents where helpful

## What to Contribute

- **Corrections** - Fix errors or outdated information
- **Clarifications** - Improve unclear instructions
- **Updates** - Update version numbers or tool changes
- **Additional troubleshooting** - Share solutions to problems you encountered

## Commit Message Convention

All commit messages should follow this format:

```
<prefix>: <description>
```

**Guidelines:**
- Keep messages brief (one line, ~50-72 characters)
- Use imperative mood ("add section" not "added section")
- Be clear about what changed and why

### Common Prefixes for Documentation

| PREFIX | DESCRIPTION                                      | EXAMPLE                                    |
|--------|--------------------------------------------------|--------------------------------------------|
| docs   | Documentation content changes                    | `docs: add troubleshooting for MSVC path`  |
| fix    | Correct errors or outdated information           | `fix: update Qt version to 6.10.1`         |
| style  | Formatting, typos (no content change)            | `style: fix markdown formatting`           |
| chore  | Maintenance tasks (links, structure)             | `chore: update cross-references`           |

### Examples

```bash
# Update documentation content
git commit -m "docs: add vcpkg installation steps"

# Fix incorrect information
git commit -m "fix: correct MSVC environment variable path"

# Fix typos or formatting
git commit -m "style: fix code block formatting"

# Restructure or maintain
git commit -m "chore: reorganize setup checklist phases"
```

## Questions or Suggestions?

Open an issue to discuss before making major changes.

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
