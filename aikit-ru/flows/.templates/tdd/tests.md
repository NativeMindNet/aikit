# Test Cases: [FEATURE_NAME]

> Version: 1.0
> Status: DRAFT | REVIEW | APPROVED
> Last Updated: [DATE]

## Overview

Test cases определённые в простом формате (Given/When/Then) перед технической спецификацией. Каждый test maps к requirement.

---

## Test: [Имя Test]

**ID**: T001
**Requirement**: [Ссылка на requirement ID]
**Type**: Functional | Edge Case | Error | Integration

### Scenario

**Given**: [Initial state/preconditions]
**When**: [Action или event]
**Then**: [Ожидаемый outcome]

### Examples

| Input | Expected Output |
|-------|-----------------|
| [value1] | [result1] |
| [value2] | [result2] |

### Edge Cases

- [Special condition 1]
- [Special condition 2]

---

## Test: [Имя Test]

**ID**: T002
**Requirement**: [Ссылка на requirement ID]
**Type**: Functional | Edge Case | Error | Integration

### Scenario

**Given**: [Initial state/preconditions]
**When**: [Action или event]
**Then**: [Ожидаемый outcome]

### Examples

| Input | Expected Output |
|-------|-----------------|
| [value1] | [result1] |

---

## Integration Flow: [Имя Flow]

End-to-end test across multiple components:

```
[Step 1] --> [Step 2] --> [Step 3] --> [Result]
```

### Scenario

**Given**: [Initial system state]
**When**: [User completes full flow]
**Then**: [Final expected state]

### Steps

1. [Действие на step 1]
2. [Действие на step 2]
3. [Действие на step 3]
4. [Verify result]

---

## Error Scenarios

### Test: [Имя Error Case]

**ID**: E001
**Requirement**: [Ссылка на requirement ID]

**Given**: [Precondition который ведёт к error]
**When**: [Действие которое триггерит error]
**Then**:
- System displays error message: "[message]"
- System state остаётся unchanged
- User can [recovery action]

---

## Test Coverage Matrix

| Requirement ID | Test IDs | Status |
|----------------|----------|--------|
| R001 | T001, T002 | Covered |
| R002 | T003 | Covered |
| R003 | - | Not Covered |

---

## Notes

- [Testing considerations]
- [Assumptions made]
- [Dependencies на external systems]

---

## Approval

- [ ] Reviewed by: [name]
- [ ] Approved on: [date]
- [ ] Notes: [любые условия или уточнения]
