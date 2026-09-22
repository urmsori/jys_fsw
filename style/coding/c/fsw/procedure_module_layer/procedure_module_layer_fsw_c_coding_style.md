# procedure module layer

## purpose

- define dependency rules between procedure layer and module layer.

## effect

- prevent circular dependency.
- module count shows abstraction level.
- procedure count shows workflow complexity.
- module/procedure ratio shows design focus.

## layer

- procedure (p0, p1, p2, ...): top-down. p0 is top.
- module (m0, m1, m2, ...): bottom-up. m0 is bottom.

## component

- layer contains components.
- component is a noun.
- action: what is done.
- thing: what is there.
- procedure component: an action. (init, loop, interrupt)
- module component: a thing. (cpu, ram, rom)

## dependency rule

1. pX can include pY. (Y > X)
2. mX can include mY. (Y < X)
3. pX can include mY. (any Y)
4. mX cannot include pY. (any Y)

```mermaid
graph LR
    P0[p0] --> P1[p1]
    P1 --> P2[p2]
    P2 --> PN[p...]
    MN[m...] --> M1[m1]
    M1 --> M0[m0]
    P0 --> MN
    P0 --> M1
    P0 --> M0
    P1 --> MN
    P1 --> M1
    P1 --> M0
    P2 --> MN
    P2 --> M1
    P2 --> M0
    PN --> MN
    PN --> M1
    PN --> M0
```

## directory structure

```
p0/
    {action}/
p1/
    {action}/
p.../
    {action}/
m0/
    {thing}/
m1/
    {thing}/
m.../
    {thing}/
```

```mermaid
graph TD
    A[p0] --> B["{action}"]
    C[p1] --> D["{action}"]
    E["p..."] --> F["{action}"]
    G[m0] --> H["{thing}"]
    I[m1] --> J["{thing}"]
    K["m..."] --> L["{thing}"]
    
    A -.-> C -.-> E -.-> K -.-> I -.-> G
```
