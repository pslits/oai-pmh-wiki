# Directory Structure
## Overview
This structure adopts the option 1, which places the core application logic (Domain, Use Cases, and Ports) inside the `/src` directory, while keeping framework-specific adapters and technical infrastructure outside it. This approach emphasizes separation of concerns, allowing the core logic to remain framework-agnostic and easily testable.

```pgsql
Project Root
│
├── /src
│   ├── Domain
│   ├── UseCase
│   └── Port
│
├── /interfaces
├── /infrastructure
└── /tests
```

## Directory Structure Breakdown

| Layer | Location | Notes |
|-------|----------|-------|
| Domain | `/src/Domain` | Core business rules and entities (protocol concepts like MetadataFormat) |
| Use Cases | `/src/UseCase` | Application logic, OAI-PMH operations (Identify, ListMetadataFormats) |
| Ports | `/src/Port` | Input/Output boundaries (interfaces for Controllers, Repos, Validators) |
| Interfaces | `/interfaces/` | Adapters: Controllers, HTTP, CLI (depends on Port/In) |
| Infrastructure | `/infrastructure/` | Implementations of Port/Out interfaces (XML Schema, Persistence, etc.) |

## Why This Structure Works
| Reason | Details |
|--------|---------|
| Separation of Concerns | Keeps domain logic free from technical dependencies. Domain and application logic live in `/src`, while adapters (web, persistence) live outside. |
| Framework Independence | Core can be extracted into a standalone package. This is particularly useful for protocol implementations like OAI-PMH. |
| Dependency Rule Compliance | Ensures that dependencies point inward only. Infrastructure and interfaces depend on core logic — never the reverse. |
| Clarity and Maintainability | Encourages a clean boundary between business logic and I/O mechanisms, simplifying refactoring and testing. |
| Protocol-Centric Design | OAI-PMH operations are modeled as clear Use Cases (Identify, GetRecord, etc.), making the architecture explicitly reflect the protocol's structure. |

## Why Other Options Were Rejected
Option 2 — All layers under `/src`

| Reason for Rejection | Explanation
|----------------------|-------------|
| Mixed Concerns | Placing infrastructure and interfaces under `/src` dilutes the core's purity and increases coupling. |
| Breaks Package Independence | Including adapters and external dependencies inside `/src` makes it harder to reuse core logic. |

Option 2 — Vertical Slices by Feature
| Reason for Rejection | Explanation
|----------------------|-------------|
| Premature Complexity | This project is focused on a specific protocol (OAI-PMH) with limited operations, making feature slicing excessive. |
| Unnecessary Overhead | The OAI-PMH protocol has a limited set of operations, so slicing by feature would introduce unnecessary complexity for such a focused implementation. |

## Architectural Flow

```plaintext
[ Interfaces (Web, CLI, etc.) ]
        ↓
[ Port/In Interfaces ]
        ↓
[ UseCase (Application Logic) ]
        ↓
[ Domain (Core Business Entities) ]
        ↓
[ Port/Out Interfaces ]
        ↓
[ Infrastructure (e.g., XML, Persistence) ]
```
