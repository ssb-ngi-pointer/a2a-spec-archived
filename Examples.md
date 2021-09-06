# Examples

## Example 1: subapp

**New app wants to be a subfeed of the root meta feed, AND wants that subfeed to be added as a member in rooms AND wants to partially replicate the main feed (contact graph).**

```mermaid
sequenceDiagram
  participant new as New SSB App
  participant os as Operating System
  participant old as Old SSB App
  participant room as Room 2.0 server

  Note over new: Present "Sign in" button
  new->>os: ssb:experimental?action=a2a&<br/>token=ABADCAFE&<br/>multiserverAddress=newappMSAddr&<br/>roomMembership=1&<br/>newSubfeed=1&<br/>replicateContacts=1
  os->>old: SSB URI
  old->>old: create subfeed `X`<br/> under meta feed
  old->>room: muxrpc: add member
  room-->>old: done
  old->>new: open muxrpc a2a duplex<br/>with token `ABADCAFE`
  activate old
  activate new
  old-->>new: info package containing<br/>subfeed keys `X` and<br/>`main` contact messages
  new-->>old: close muxrpc a2a duplex
  deactivate new
  deactivate old
```

## Example 2: migration

**New app wants to take over everything from the old app and the old one should be deleted afterwards.**

```mermaid
sequenceDiagram
  participant new as New SSB App
  participant os as Operating System
  participant old as Old SSB App

  Note over new: Present "Migrate" button
  new->>os: ssb:experimental?action=a2a&<br/>token=ABADCAFE&<br/>multiserverAddress=newappMSAddr&<br/>everything=1
  os->>old: SSB URI
  old->>new: open muxrpc a2a duplex<br/>with token `ABADCAFE`
  activate old
  activate new
  old-->>new: muxrpc: info package containing<br />main keys `X`
  new-->>old: createHistoryStream(main)
  old-->>new: main feed messages
  old->>old: delete own database
  old-->>new: close muxrpc a2a duplex
  deactivate new
  deactivate old
```

## Example 3: fusion dance

**New app and old app want to link each other as the same "person".**

```mermaid
sequenceDiagram
  participant new as New SSB App
  participant os as Operating System
  participant old as Old SSB App

  Note over new: Present "Link" button
  new->>os: ssb:experimental?action=a2a&<br/>token=ABADCAFE&<br/>multiserverAddress=newappMSAddr&<br/>fusion=ssb:feed/ed25519/new
  os->>old: SSB URI
  old->>old: publish fusion/init
  old->>old: publish fusion/invite for<br/>ssb:feed/ed25519/new
  old->>new: open muxrpc a2a duplex<br/>with token `ABADCAFE`
  activate old
  activate new
  old-->>new: info package containing<br />the two fusion messages
  new->>new: publish fusion/consent
  new-->>old: my fusion/consent message
  old->>old: publish fusion/entrust
  old-->>new: my fusion/entrust message
  new->>new: proof-of-key
  new-->>old: close muxrpc a2a duplex
  deactivate new
  deactivate old
```
