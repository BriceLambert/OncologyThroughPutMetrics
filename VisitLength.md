---
config:
  layout: elk
---
flowchart TB
    A["Start: Calculate Visit Length"] --> Adj["AdjustedRoomingDTS:<br>RoomingDTS >= CheckInDTS?<br>Yes - RoomingDTS<br>No - NULL"]
    Adj --> SDec["ChairTime Start (for validation):<br>TxStartedDTS not null<br>AND TxStartedDTS >= AdjustedRoomingDTS?<br>Else is AdjustedRoomingDTS not null?"]
    SDec --> EDec["Visit End Time Priority:<br>TxCompleteDTS not null<br>AND (ChairTimeStartDTS null OR TxCompleteDTS > ChairTimeStartDTS)<br>AND (AdjustedRoomingDTS null OR TxCompleteDTS > AdjustedRoomingDTS)?"]
    EDec -- Yes --> E1["VisitLengthEndDTS = TxCompleteDTS<br>- Prioritize (Treatment Complete)"]
    EDec -- No --> E2["VisitEndDTS not null<br>AND (ChairTimeStartDTS null OR VisitEndDTS > ChairTimeStartDTS)<br>AND (AdjustedRoomingDTS null OR VisitEndDTS > AdjustedRoomingDTS)?"]
    E2 -- Yes --> E3["VisitLengthEndDTS = VisitEndDTS<br>- Fallback (Visit Wrapped Up)"]
    E2 -- No --> E4["PtDischargeDTS not null<br>AND (ChairTimeStartDTS null OR PtDischargeDTS > ChairTimeStartDTS)<br>AND (AdjustedRoomingDTS null OR PtDischargeDTS > AdjustedRoomingDTS)?"]
    E4 -- Yes --> E5["VisitLengthEndDTS = PtDischargeDTS<br>- Fallback II (Patient Discharge)"]
    E4 -- No --> NullE["No Valid Visit End"]
    E1 --> V["VisitLengthEndDTS not null<br>AND VisitLengthEndDTS > CheckInDTS?"]
    E3 --> V
    E5 --> V
    NullE --> NullC["VisitLengthNBR = NULL"]
    V -- Yes --> Diff["VisitLengthNBR = DATEDIFF minute<br>CheckInDTS to VisitLengthEndDTS<br>- Positive Value"]
    V -- No --> NullC
    Diff --> End["End: Visit Length Output<br>NULL if no valid end time<br>NULL if End <= CheckIn<br>Positive minutes otherwise"]
    NullC --> End
    SDec@{ shape: hex}
    EDec@{ shape: hex}
    E2@{ shape: hex}
    E4@{ shape: hex}
    V@{ shape: hex}
    Adj@{ shape: hex}
    style A fill:#e1f5fe
    style NullE fill:#fff3e0
    style NullC fill:#fff3e0
    style End fill:#e8f5e8


    %% linkStyle 2 stroke:#4caf50,stroke-width:3px,fill:none     
    linkStyle 3 stroke:#4caf50,stroke-width:3px,fill:none   
    linkStyle 4 stroke:#f44336,stroke-width:2px,stroke-dasharray:5,5,fill:none  
    linkStyle 5 stroke:#4caf50,stroke-width:3px,fill:none   
    linkStyle 6 stroke:#f44336,stroke-width:2px,stroke-dasharray:5,5,fill:none 
    linkStyle 7 stroke:#4caf50,stroke-width:3px,fill:none 
    linkStyle 8 stroke:#f44336,stroke-width:2px,stroke-dasharray:5,5,fill:none 
    linkStyle 13 stroke:#4caf50,stroke-width:3px,fill:none   
    linkStyle 14 stroke:#f44336,stroke-width:2px,stroke-dasharray:5,5,fill:none 
