# Webhook authentication

Use the complete descriptor schema below to construct `config.auth` as a JSON object. It covers anonymous admission (`none`), Typenode access keys (`access_key`), static shared secrets and signed requests. Choose the mode required by the caller's verified protocol and the member's authorization. Secret-backed modes reference existing static secrets by `secret_name`; their values enter through the trusted handoff.

## Signed requests

`signed_string` is the exact message template: `{body}` uses the raw request body, `{timestamp}` uses the configured timestamp source, `{url}` uses the request URL, `{method}` uses its method, `{form_params_sorted}` uses sorted form parameters, `{header:NAME}` uses a header and `{field:alias}` uses a declared `json_fields` projection. Escape literal braces as `{{` and `}}`. Use the provider's actual signing recipe, algorithm, encoding, extraction and secret transform.

The runtime checks relationships that the structural schema cannot express:

- Basic authentication requires `transform:"base64"`.
- Sign request data. HMAC uses the secret as its key; plain hashes include `{secret}` in the message.
- A configured timestamp must be signed exactly. A pair timestamp needs header pair extraction; per-key signature headers cannot use pair timestamps.
- Scheme extraction and per-key signature-header overrides need a header source.
- `body_digest` requires signed `{url}` and cannot combine with `url_percent_decode`.

## Correcting a refusal

Use validation errors' field paths and messages to correct the descriptor while preserving the intended authentication policy. For a generic union error, consult this schema and the verified caller protocol before retrying. A failed descriptor is not authorization to switch to anonymous admission. `update_trigger` replaces the entire auth descriptor, so a repair sends the complete corrected object.

## Descriptor schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "oneOf": [
    {
      "type": "object",
      "properties": {
        "kind": {
          "type": "string",
          "const": "none"
        }
      },
      "required": [
        "kind"
      ],
      "additionalProperties": false
    },
    {
      "type": "object",
      "properties": {
        "kind": {
          "type": "string",
          "const": "access_key"
        }
      },
      "required": [
        "kind"
      ],
      "additionalProperties": false
    },
    {
      "type": "object",
      "properties": {
        "kind": {
          "type": "string",
          "const": "static_secret"
        },
        "location": {
          "oneOf": [
            {
              "type": "object",
              "properties": {
                "in": {
                  "type": "string",
                  "const": "header"
                },
                "name": {
                  "type": "string",
                  "minLength": 1,
                  "pattern": "^[!#$%&'*+.^_`|~0-9A-Za-z-]+$"
                },
                "scheme": {
                  "type": "string",
                  "enum": [
                    "Bearer",
                    "Basic"
                  ]
                }
              },
              "required": [
                "in",
                "name"
              ],
              "additionalProperties": false
            },
            {
              "type": "object",
              "properties": {
                "in": {
                  "type": "string",
                  "const": "query"
                },
                "name": {
                  "type": "string",
                  "minLength": 1
                }
              },
              "required": [
                "in",
                "name"
              ],
              "additionalProperties": false
            },
            {
              "type": "object",
              "properties": {
                "in": {
                  "type": "string",
                  "const": "body"
                },
                "path": {
                  "type": "string",
                  "minLength": 1,
                  "description": "Slash-leading RFC 6901 JSON Pointer for JSON bodies, or a non-slash literal field name for form-encoded bodies"
                }
              },
              "required": [
                "in",
                "path"
              ],
              "additionalProperties": false
            }
          ]
        },
        "transform": {
          "type": "string",
          "enum": [
            "raw",
            "base64"
          ]
        },
        "secrets": {
          "minItems": 1,
          "maxItems": 8,
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "secret_name": {
                "type": "string",
                "minLength": 1,
                "maxLength": 100,
                "pattern": "^[A-Z][A-Z0-9_]*$"
              }
            },
            "required": [
              "secret_name"
            ],
            "additionalProperties": false
          }
        }
      },
      "required": [
        "kind",
        "location",
        "transform",
        "secrets"
      ],
      "additionalProperties": false
    },
    {
      "type": "object",
      "properties": {
        "kind": {
          "type": "string",
          "const": "hmac"
        },
        "signature": {
          "type": "object",
          "properties": {
            "from": {
              "anyOf": [
                {
                  "type": "object",
                  "properties": {
                    "header": {
                      "type": "string",
                      "minLength": 1,
                      "pattern": "^[!#$%&'*+.^_`|~0-9A-Za-z-]+$"
                    }
                  },
                  "required": [
                    "header"
                  ],
                  "additionalProperties": false
                },
                {
                  "type": "object",
                  "properties": {
                    "body": {
                      "type": "string",
                      "minLength": 1
                    }
                  },
                  "required": [
                    "body"
                  ],
                  "additionalProperties": false
                }
              ]
            },
            "extract": {
              "oneOf": [
                {
                  "type": "object",
                  "properties": {
                    "kind": {
                      "type": "string",
                      "const": "whole"
                    }
                  },
                  "required": [
                    "kind"
                  ],
                  "additionalProperties": false
                },
                {
                  "type": "object",
                  "properties": {
                    "kind": {
                      "type": "string",
                      "const": "prefix"
                    },
                    "prefix": {
                      "type": "string",
                      "minLength": 1
                    }
                  },
                  "required": [
                    "kind",
                    "prefix"
                  ],
                  "additionalProperties": false
                },
                {
                  "type": "object",
                  "properties": {
                    "kind": {
                      "type": "string",
                      "const": "scheme"
                    },
                    "scheme": {
                      "type": "string",
                      "minLength": 1,
                      "pattern": "^[!#$%&'*+.^_`|~0-9A-Za-z-]+$"
                    }
                  },
                  "required": [
                    "kind",
                    "scheme"
                  ],
                  "additionalProperties": false
                },
                {
                  "type": "object",
                  "properties": {
                    "kind": {
                      "type": "string",
                      "const": "pairs"
                    },
                    "sep": {
                      "type": "string",
                      "enum": [
                        ",",
                        ";"
                      ]
                    },
                    "key": {
                      "type": "string",
                      "minLength": 1
                    }
                  },
                  "required": [
                    "kind",
                    "sep",
                    "key"
                  ],
                  "additionalProperties": false
                },
                {
                  "type": "object",
                  "properties": {
                    "kind": {
                      "type": "string",
                      "const": "list"
                    },
                    "sep": {
                      "type": "string",
                      "enum": [
                        ",",
                        " "
                      ]
                    },
                    "prefix": {
                      "type": "string",
                      "minLength": 1
                    }
                  },
                  "required": [
                    "kind",
                    "sep",
                    "prefix"
                  ],
                  "additionalProperties": false
                }
              ]
            }
          },
          "required": [
            "from",
            "extract"
          ],
          "additionalProperties": false
        },
        "signed_string": {
          "type": "string"
        },
        "algorithm": {
          "type": "string",
          "enum": [
            "hmac-sha1",
            "hmac-sha256",
            "hmac-sha512",
            "sha256-hash",
            "sha1-hash"
          ]
        },
        "encoding": {
          "type": "string",
          "enum": [
            "hex",
            "base64",
            "base64url"
          ]
        },
        "secret_transform": {
          "anyOf": [
            {
              "type": "string",
              "enum": [
                "raw",
                "base64",
                "hex"
              ]
            },
            {
              "type": "object",
              "properties": {
                "strip_prefix": {
                  "type": "string",
                  "minLength": 1
                },
                "then": {
                  "type": "string",
                  "const": "base64"
                }
              },
              "required": [
                "strip_prefix",
                "then"
              ],
              "additionalProperties": false
            }
          ]
        },
        "secrets": {
          "minItems": 1,
          "maxItems": 8,
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "secret_name": {
                "type": "string",
                "minLength": 1,
                "maxLength": 100,
                "pattern": "^[A-Z][A-Z0-9_]*$"
              },
              "signature_header": {
                "type": "string",
                "minLength": 1,
                "pattern": "^[!#$%&'*+.^_`|~0-9A-Za-z-]+$"
              }
            },
            "required": [
              "secret_name"
            ],
            "additionalProperties": false
          }
        },
        "timestamp": {
          "type": "object",
          "properties": {
            "from": {
              "anyOf": [
                {
                  "anyOf": [
                    {
                      "type": "object",
                      "properties": {
                        "header": {
                          "type": "string",
                          "minLength": 1,
                          "pattern": "^[!#$%&'*+.^_`|~0-9A-Za-z-]+$"
                        }
                      },
                      "required": [
                        "header"
                      ],
                      "additionalProperties": false
                    },
                    {
                      "type": "object",
                      "properties": {
                        "body": {
                          "type": "string",
                          "minLength": 1
                        }
                      },
                      "required": [
                        "body"
                      ],
                      "additionalProperties": false
                    }
                  ]
                },
                {
                  "type": "object",
                  "properties": {
                    "pairs_key": {
                      "type": "string",
                      "minLength": 1
                    }
                  },
                  "required": [
                    "pairs_key"
                  ],
                  "additionalProperties": false
                }
              ]
            },
            "format": {
              "type": "string",
              "enum": [
                "unix_s",
                "unix_ms",
                "rfc3339"
              ]
            },
            "tolerance_seconds": {
              "type": "integer",
              "minimum": 0,
              "maximum": 86400
            }
          },
          "required": [
            "from",
            "format",
            "tolerance_seconds"
          ],
          "additionalProperties": false
        },
        "url_percent_decode": {
          "type": "array",
          "items": {
            "type": "string",
            "pattern": "^[0-9A-F]{2}$"
          }
        },
        "required_headers": {
          "maxItems": 16,
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "name": {
                "type": "string",
                "minLength": 1,
                "pattern": "^[!#$%&'*+.^_`|~0-9A-Za-z-]+$"
              },
              "value": {
                "type": "string"
              }
            },
            "required": [
              "name",
              "value"
            ],
            "additionalProperties": false
          }
        },
        "body_digest": {
          "type": "object",
          "properties": {
            "algorithm": {
              "type": "string",
              "const": "sha256"
            },
            "encoding": {
              "type": "string",
              "const": "hex"
            },
            "compare_to": {
              "type": "object",
              "properties": {
                "query": {
                  "type": "string",
                  "minLength": 1
                }
              },
              "required": [
                "query"
              ],
              "additionalProperties": false
            }
          },
          "required": [
            "algorithm",
            "encoding",
            "compare_to"
          ],
          "additionalProperties": false
        },
        "json_array_lengths": {
          "maxItems": 16,
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "path": {
                "type": "string",
                "minLength": 1
              },
              "length": {
                "type": "integer",
                "minimum": 0,
                "maximum": 9007199254740991
              }
            },
            "required": [
              "path",
              "length"
            ],
            "additionalProperties": false
          }
        },
        "json_fields": {
          "type": "object",
          "propertyNames": {
            "type": "string"
          },
          "additionalProperties": {
            "type": "object",
            "properties": {
              "path": {
                "type": "string",
                "minLength": 1
              },
              "scalar": {
                "type": "string",
                "enum": [
                  "string",
                  "integer"
                ]
              },
              "missing": {
                "type": "string",
                "enum": [
                  "reject",
                  "empty"
                ]
              },
              "escape": {
                "type": "string",
                "enum": [
                  "none",
                  "colon_backslash"
                ]
              }
            },
            "required": [
              "path",
              "scalar",
              "missing",
              "escape"
            ],
            "additionalProperties": false
          }
        }
      },
      "required": [
        "kind",
        "signature",
        "signed_string",
        "algorithm",
        "encoding",
        "secret_transform",
        "secrets"
      ],
      "additionalProperties": false
    }
  ]
}
```
