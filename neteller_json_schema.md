## Neteller JSON schema
---
```json
JSON SCHEMA {
  "$schema": "http://json-schema.org/draft-04/schema#",
  "type": "object",
  "properties": {
    "customer": {
      "type": "object",
      "properties": {},
      "description": "The associated customer information for the payment."
    },
    "transaction": {
      "type": "object",
      "properties": {},
      "description": "Details about the payment."
    },
    "links": {
      "type": "string",
      "description": "Array of HATEOAS links (if applicable)."
    }
  },
  "required": [
    "customer",
    "transaction"
  ]
}
```
