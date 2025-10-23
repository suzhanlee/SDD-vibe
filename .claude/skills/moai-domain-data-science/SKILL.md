---
name: moai-domain-data-science
version: 2.0.0
created: 2025-10-22
updated: 2025-10-22
status: active
description: Data analysis, visualization, statistical modeling, and reproducible research workflows.
keywords: ['data', 'analysis', 'visualization', 'statistics', 'jupyter']
allowed-tools:
  - Read
  - Bash
---

# Domain Data Science Skill

## Skill Metadata

| Field | Value |
| ----- | ----- |
| **Skill Name** | moai-domain-data-science |
| **Version** | 2.0.0 (2025-10-22) |
| **Allowed tools** | Read (read_file), Bash (terminal) |
| **Auto-load** | On demand when keywords detected |
| **Tier** | Domain |

---

## What It Does

Data analysis, visualization, statistical modeling, and reproducible research workflows.

**Key capabilities**:
- ✅ Best practices enforcement for domain domain
- ✅ TRUST 5 principles integration
- ✅ Latest tool versions (2025-10-22)
- ✅ TDD workflow support

---

## When to Use

**Automatic triggers**:
- Related code discussions and file patterns
- SPEC implementation (`/alfred:2-run`)
- Code review requests

**Manual invocation**:
- Review code for TRUST 5 compliance
- Design new features
- Troubleshoot issues

---

## Tool Version Matrix (2025-10-22)

| Tool | Version | Purpose | Status |
|------|---------|---------|--------|
| **Pandas** | 2.2.0 | Primary | ✅ Current |
| **NumPy** | 2.2.0 | Primary | ✅ Current |
| **Jupyter** | 1.1.0 | Primary | ✅ Current |

---

## Inputs

- Language-specific source directories
- Configuration files
- Test suites and sample data

## Outputs

- Test/lint execution plan
- TRUST 5 review checkpoints
- Migration guidance

## Failure Modes

- When required tools are not installed
- When dependencies are missing
- When test coverage falls below 85%

## Dependencies

- Access to project files via Read/Bash tools
- Integration with `moai-foundation-langs` for language detection
- Integration with `moai-foundation-trust` for quality gates

---

## References (Latest Documentation)

_Documentation links updated 2025-10-22_

---

## Changelog

- **v2.0.0** (2025-10-22): Major update with latest tool versions, comprehensive best practices, TRUST 5 integration
- **v1.0.0** (2025-03-29): Initial Skill release

---

## Works Well With

- `moai-foundation-trust` (quality gates)
- `moai-alfred-code-reviewer` (code review)
- `moai-essentials-debug` (debugging support)

---

## Best Practices

✅ **DO**:
- Follow domain best practices
- Use latest stable tool versions
- Maintain test coverage ≥85%
- Document all public APIs

❌ **DON'T**:
- Skip quality gates
- Use deprecated tools
- Ignore security warnings
- Mix testing frameworks
