# Orbit Protocol v1.0 Specification

**Document Status:** Final & Frozen  
**Release Date:** January 01, 2026  
**Specification Version:** 1.0.0

---

## Executive Summary

Orbit Protocol v1.0 defines a minimal, portable format for encoding single-action intents as JSON objects. The specification is deliberately constrained to ensure unambiguous interpretation across implementations while maintaining forward compatibility.

---

## Key Design Principles

1. **Simplicity Over Power:** One message, one action, no ambiguity  
2. **Implementation Agnostic:** Describes what, not how  
3. **Fail-Closed:** Invalid messages are rejected, not interpreted  
4. **Forward Compatible:** Unknown versions are safely ignored

---

## Specification Highlights

### Core Message Format

```json
{
  "orbit": "1.0",
  "id": "unique-identifier",
  "action": "token-from-vocabulary",
  "payload": {}
}
```

### Locked Action Vocabulary (v1.0)

- **open**: Intent to open a resource (requires `url` in payload)  
- **clipboard**: Intent to copy text to the clipboard (requires `text` in payload)  
- **wifi**: Intent to connect to a Wi-Fi network (requires `ssid` in payload)

### Validation Rules

- Only JSON objects with exactly four defined fields  
- No extra fields allowed  
- Strict version checking (`"1.0"` only)  
- Action token must match the locked vocabulary  
- Payload must conform to the action-specific schema

---

## 1. Purpose

Orbit is a protocol for encoding **exactly one intended action** in a portable, implementation-agnostic data format.

Orbit describes intent only. Orbit does not execute actions.

---

## 2. Non-Goals (Out of Scope)

Orbit v1.0 does **not** define or include:

- Execution logic  
- Permissions or authentication  
- Scheduling or timing  
- Conditional logic or workflows  
- Device, OS, or hardware behavior  
- Networking or transport mechanisms  
- UI/UX or user confirmation requirements  
- Error recovery or retries

---

## 3. Core Rules (Normative)

1. **One Orbit message represents exactly one action.**  
2. Orbit messages **must** be valid JSON objects.  
3. Only the fields defined in this specification are allowed.  
4. Unknown or unsupported versions **must** be ignored.  
5. Invalid messages **must not** be executed.  
6. Orbit is **fail-closed**, not permissive.

---

## 4. Data Model

An Orbit message is a JSON object with **exactly** four fields:

```json
{
  "orbit": "1.0",
  "id": "string",
  "action": "string",
  "payload": {}
}
```

### Field Definitions (Normative)

| Field | Type | Required | Description |
|---|---|---:|---|
| `orbit` | string | yes | Protocol version; must equal `"1.0"` |
| `id` | string | yes | Unique identifier for this message |
| `action` | string | yes | Action token from the locked vocabulary |
| `payload` | object | yes | Action-specific payload object |

---

## 5. Versioning (Normative)

- `orbit` **must** be a string.
- Only `"1.0"` is valid for this specification.

### Version Handling

| Case | Required Behavior |
|---|---|
| `orbit == "1.0"` | Validate and continue |
| Unknown version | Ignore message entirely |
| Missing `orbit` | Reject as invalid |
| Non-string `orbit` | Reject as invalid |

Unknown versions are ignored silently and safely to preserve forward compatibility.

---

## 6. Identifier (`id`) (Normative)

- Must be a string.
- Format is not enforced by the protocol.
- Must be unique within the execution context.

Orbit does not interpret or generate IDs.

---

## 7. Locked Action Vocabulary (Normative)

Orbit v1.0 defines a closed set of actions. No aliases, inference, or guessing is allowed.

### 7.1 `open`

Intent to open a resource.

Payload schema:

```json
{
  "url": "string"
}
```

Rules:
- `url` is required and must be a string.

### 7.2 `clipboard`

Intent to place text into the clipboard.

Payload schema:

```json
{
  "text": "string"
}
```

Rules:
- `text` is required and must be a string.

### 7.3 `wifi`

Intent to connect to a Wi-Fi network.

Payload schema:

```json
{
  "ssid": "string",
  "password": "string"
}
```

Rules:
- `ssid` is required and must be a string.
- `password` is optional. If present, it must be a string.

---

## 8. Payload Rules (Normative)

- `payload` must always be a JSON object.
- Payload schema is action-specific.
- Extra fields are not allowed.
- Missing required fields make the message invalid.
- Orbit does not support multiple actions, workflows, conditions, or side effects.

---

## 9. Validation Outcomes (Normative)

| Condition | Required Behavior |
|---|---|
| Valid v1.0 message | Accept and process intent |
| Invalid v1.0 message | Reject; do not execute |
| Unknown action | Reject; do not execute |
| Unknown version | Ignore message entirely |
| Malformed JSON | Reject |

Orbit defines outcomes, not error messages or return codes.

---

## 10. Execution Boundary (Normative)

Orbit ends at intent. Executors may display, log, confirm, translate, and apply platform security policies. Executors must never assume permission or execution from Orbit alone.

---

## 11. Security Considerations (Normative)

Orbit v1.0 intentionally provides no authentication, authorization, trust, encryption, or sandboxing guarantees. All security decisions are executor and platform concerns.

---

## 12. Conformance Requirements (Normative)

An implementation is Orbit v1.0 compliant if it:

1. Accepts all valid v1.0 messages.  
2. Rejects all invalid v1.0 messages.  
3. Ignores all unknown protocol versions.  
4. Enforces the locked action vocabulary.  
5. Does not extend or mutate the protocol structure.

---

## 13. Forward Compatibility

Orbit Protocol is designed to evolve without breaking existing implementations.

- Implementations must only execute messages where `orbit == "1.0"`.
- Messages with unknown or future versions must be ignored silently.
- Unknown versions must not trigger fallback behavior.

---

## 14. Change Control

Orbit Protocol v1.0 is final and frozen.

- No changes will be made to v1.0.
- Future work must occur under a new major version.
- Backward compatibility is preserved through version isolation.

---

## 15. Final Statement

Orbit Protocol v1.0 is intentionally minimal.

Any increase in expressive power increases ambiguity, implementation burden, and the risk of divergent behavior. For that reason, features beyond the scope of this document are deliberately excluded.

This specification is complete and suitable for long-term use.
