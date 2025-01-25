# Creating Architecture Diagrams

## Mermaid JS

### Installation

- Neovim: <https://github.com/3rd/diagram.nvim>.
- Visual Studio Code: there is an extension that renders mermaid directly in markdown (`.md`) files: [Markdown Preview Mermaid Support](https://marketplace.visualstudio.com/items?itemName=bierner.markdown-mermaid).
  - Note that there's a feature to export the diagram.
- A live web-based editor with ability to export diagrams: <https://mermaid.live>.

### Examples

#### Modelling flow

```mermaid
flowchart TD
    A[Christmas] -->|Get money| B(Go shopping)
    B --> C{Let me think}
    C -->|One| D[Laptop]
    C -->|Two| E[iPhone]
    C -->|Three| F[fa:fa-car Car]
```

```mermaid
flowchart LR
    S[Start] --> A
    A(Enter your email address) --> B{Existing user?}
    B --> |No| C(Enter name)
    C --> D{Accept conditions?}
    D --> |No| A
    D --> |Yes| E(Send email with magic link)
    B --> |Yes| E
    E --> End
```

#### Modelling interaction between multiple participants

```mermaid
sequenceDiagram
    autonumber
    participant Client
    participant OAuthProvider
    participant Server
    Client ->> OAuthProvider: Request access token
    activate OAuthProvider
    OAuthProvider ->> Client: Send access token
    deactivate OAuthProvider
    Client ->> Server: Request resource
    activate Server
    Server ->> OAuthProvider: Validate token
    activate OAuthProvider
    OAuthProvider ->> Server: Token valid
    deactivate OAuthProvider
    Server ->> Client: Send resource
    deactivate Server
```

#### Modelling objects with relationships

- `o--`: Aggregation (right side can exist independently without left side).
- `*--`: Composition (right side is part of left side).
- `<|--`: Inheritance.

```mermaid
classDiagram
    class Order {
        OrderStatus status
    }
    class OrderStatus {
        <<enumeration>>
        FAILED
        PENDING
        PAID
    }
    class PaymentProcessor {
        <<interface>>
        -String apiKey
        #connect(String url, JSON header)
        +processPayment(Order order) OrderStatus
    }
    class Customer {
        +String name
    }
    Customer <|-- PrivateCustomer
    Customer <|-- BusinessCustomer
    PaymentProcessor <|-- StripePaymentProcessor
    PaymentProcessor <|-- PayPalPaymentProcessor
    Order o-- Customer
    Car *-- Engine
```

#### Modelling concepts

See meaning of symbols [in the Mermaid documentation](https://mermaid.js.org/syntax/entityRelationshipDiagram.html).

```mermaid
erDiagram
    Customer ||--o{ Order : places
    Order ||--|{ LineItem : contains
    Customer {
        String id
        String name
    }
    Order {
        String id
        OrderStatus status
    }
    LineItem {
        String code
        String description
        int quantity
        int price
    }
```

## Python's [`diagrams`](https://pypi.org/project/diagrams/)

- Has built-in support for cloud architectures (for example, AWS).
- Has an equivalent in Go.

## PlantUML

- More complex than mermaid.
- Oldest of all.
- To view, use [the online server](https://www.plantuml.com/plantuml/uml/SyfFKj2rKt3CoKnELR1Io4ZDoSa700002).
- Supports [JSON data visualization](https://plantuml.com/json).

```plantuml
@startuml
autonumber
participant Client
participant OAuthProvider
participant Server
Client ->> OAuthProvider: Request access token
activate OAuthProvider
OAuthProvider ->> Client: Send access token
deactivate OAuthProvider
Client ->> Server: Request resource
activate Server
Server ->> OAuthProvider: Validate token
activate OAuthProvider
OAuthProvider ->> Server: Token valid
deactivate OAuthProvider
Server ->> Client: Send resource
deactivate Server
@enduml
```

## [`HackerDraw`](https://www.hackerdraw.com/) and [`ExcaliDraw`](https://excalidraw.com/)

- Newest of all.
- Purely web-based.
- Alternatives to drawio.
- Easiest to learn of all.
- Can easily integrate with Notion and Confluence.

## Resources

- Mermaid JS: Finally There's A Great UML & Diagram Drawing Tool: <https://www.youtube.com/watch?v=JiQmpA474BY>.
- Mermaid vs PlantUML vs HackerDraw: Which One Is Best For You?: <https://www.youtube.com/watch?v=tPh9_Cx4yZY>.
- Top 6 Tools to Turn Code into Beautiful Diagrams: <https://www.youtube.com/watch?v=jCd6XfWLZsg>.
