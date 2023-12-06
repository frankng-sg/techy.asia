---
title: How to code Sequence Diagram
author: Frank Nguyen
pubDatetime: 2023-12-06T22:00:00+08:00
postSlug: how-to-code-sequence-diagram
featured: false
draft: false
tags:
  - How-To
  - Sequence-Diagram
  - System-Design
description: A simple guide to draw complex Sequence Diagram by coding
---

Developers frequently employ Sequence Diagrams to depict the interactions among micro-services. These diagrams illustrate how diverse services collaborate to accomplish a specific task. As the sequence diagram undergoes modifications with each system design iteration, manually redrawing the diagram becomes laborious and inefficient. To enhance productivity, [PlantUML](https://plantuml.com/sequence-diagram) allows developers to articulate service interactions through code. The tool then seamlessly transforms the code into a visual diagram. This approach ensures that any alterations in the initial system design result in minimal changes to the code, facilitating swift updates and diagram regeneration.

## Sample Authentication Workflow

![](@assets/sample-sequence-diagram-00.png)

## How to implement by code

**Code**:

```
@startuml
Employee -> LoginService: login(userName, userPass)
LoginService -> AuthService: request_access(userName, userPass)
AuthService --> LoginService: request is rejected
LoginService --> Employee: fail to login
@enduml
```

**Remark**: <br />
-> is solid line arrow that represents a request <br />

\- \-> is dashed line arrow that represents a response <br />

**Generate Diagram**: <br />
Copy above code into this [PlantUML Web Server](http://www.plantuml.com/plantuml/uml/) and click `Submit`
