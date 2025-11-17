---
config:
  layout: elk
---
flowchart TB
    A["Start: Calculate Chair Time"] --> Adj["AdjustedRoomingDTS:<br>RoomingDTS &gt;= CheckInDTS?<br>Yes - RoomingDTS<br>No - NULL"]
    Adj -- Yes --> SDec["ChairTime Start: 
                        TxStartedDTS not null<br>AND TxStartedDTS &gt;= AdjustedRoomingDTS?"]
    Adj -- No --> NullS["No Valid Chair Start"]
    SDec -- Yes --> S1["ChairTimeStartDTS = TxStartedDTS<br>- Prioritize"]
    SDec -- No --> S2["AdjustedRoomingDTS<br>IS NOT NULL?"]
    S2 -- Yes --> S3["ChairTimeStartDTS = AdjustedRoomingDTS<br>- Fallback"]
    S2 -- No --> NullS
    S1 --> EDec["Chairtime End : <br> TxCompleteDTS not null<br>AND TxCompleteDTS &gt;= ChairTimeStartDTS<br><br>AND (AdjustedRoomingDTS null OR TxCompleteDTS &gt; AdjustedRoomingDTS)?"]
    S3 --> EDec
    EDec -- Yes --> E1["ChairTimeEndDTS = TxCompleteDTS<br>- Prioritize"]
    EDec -- No --> E2["VisitEndDTS not null<br>AND VisitEndDTS &gt;= ChairTimeStartDTS<br>AND (AdjustedRoomingDTS null OR VisitEndDTS &gt; AdjustedRoomingDTS)?"]
    E2 -- Yes --> E3["ChairTimeEndDTS = VisitEndDTS<br>- Fallback"]
    E2 -- No --> NullE["No Valid Chair End"]
    E1 --> V["ChairTimeEndDTS not null<br>AND ChairTimeStartDTS not null<br>AND ChairTimeEndDTS &gt; ChairTimeStartDTS?"]
    E3 --> V
    NullS --> NullC["ChairTimeNBR = NULL"]
    NullE --> NullC
    V -- Yes --> Diff["ChairTimeNBR = DATEDIFF minute<br>ChairTimeStartDTS to ChairTimeEndDTS<br>- Positive Value"]
    V -- No --> NullC
    Diff --> End["End: Chair Time Output<br>NULL if Start or End NULL<br>NULL if End &lt;= Start<br>Positive minutes otherwise"]
    NullC --> End
    SDec@{ shape: hex}
    S2@{ shape: hex}
    EDec@{ shape: hex}
    E2@{ shape: hex}
    V@{ shape: hex}
    style A fill:#e1f5fe
    style NullS fill:#fff3e0
    style NullE fill:#fff3e0
    style NullC fill:#fff3e0
    style End fill:#e8f5e8

    linkStyle 1 stroke:#4caf50,stroke-width:3px,fill:none  
    linkStyle 2 stroke:#f44336,stroke-width:2px,stroke-dasharray:5,5,fill:none
    linkStyle 3 stroke:#4caf50,stroke-width:3px,fill:none
    linkStyle 4 stroke:#f44336,stroke-width:2px,stroke-dasharray:5,5,fill:none
    linkStyle 5 stroke:#4caf50,stroke-width:3px,fill:none
    linkStyle 6 stroke:#f44336,stroke-width:2px,stroke-dasharray:5,5,fill:none
    linkStyle 7 stroke:#4caf50,stroke-width:3px,fill:none
 
    linkStyle 9 stroke:#4caf50,stroke-width:3px,fill:none
    linkStyle 10 stroke:#f44336,stroke-width:2px,stroke-dasharray:5,5,fill:none
    linkStyle 11 stroke:#4caf50,stroke-width:3px,fill:none
    linkStyle 12 stroke:#f44336,stroke-width:2px,stroke-dasharray:5,5,fill:none
    linkStyle 13 stroke:#4caf50,stroke-width:3px,fill:none
    linkStyle 1 stroke:#4caf50,stroke-width:3px,fill:none
    linkStyle 18 stroke:#f44336,stroke-width:2px,stroke-dasharray:5,5,fill:none 