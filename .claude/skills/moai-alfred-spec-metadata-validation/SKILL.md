---
name: moai-alfred-spec-metadata-validation
version: 2.0.0
created: 2025-10-22
updated: 2025-10-22
status: active
description: SPEC YAML frontmatter validation with 7 required fields and HISTORY section compliance.
keywords: ['spec', 'validation', 'metadata', 'yaml']
allowed-tools:
  - Read
  - Bash
---

# Alfred Spec Metadata Validation Skill

## Skill Metadata

| Field | Value |
| ----- | ----- |
| **Skill Name** | moai-alfred-spec-metadata-validation |
| **Version** | 2.0.0 (2025-10-22) |
| **Allowed tools** | Read (read_file), Bash (terminal) |
| **Auto-load** | On demand when keywords detected |
| **Tier** | Alfred |

---

## What It Does

SPEC YAML frontmatter validation with 7 required fields and HISTORY section compliance.

**Key capabilities**:
- ✅ Best practices enforcement for alfred domain
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
- Follow alfred best practices
- Use latest stable tool versions
- Maintain test coverage ≥85%
- Document all public APIs

❌ **DON'T**:
- Skip quality gates
- Use deprecated tools
- Ignore security warnings
- Mix testing frameworks
