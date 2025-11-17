

---
config:
  layout: elk
id: e4be1c7f-3c7d-41a3-a3a0-31ab12fe2ae1
---
flowchart TB
    A["Start: Calculate Initial Only Wait Time"] --> B["AdjustedRoomingDTS :
          RoomingDTS >= CheckInDTS
         Yes - RoomingDTS
         No - NULL"]
    B -- yes --> CalcEnds["Calculate ChairTimeEndDTS and VisitLengthEndDTS"]
    B -- No --> NullW["Invalid or No RoomingDTS"]
    CalcEnds --> V{"AdjustedRoomingDTS &lt; ChairTimeEndDTS 
                    and AdjustedRoomingDTS &lt;  VisitLengthEndDTS"}
    V -- No --> NullW
    V -- Yes --> I@{ label: "InitialEncounterFLG = 'Y' OR IS NULL?" }
    I -- Yes --> Diff["InitialOnlyWaitTimeNBR = Number of minutes from  CheckInDTS to AdjustedRoomingDTS"]
    I -- No --> NullI["Not Initial Encounter"]
    NullW --> NULL["InitialOnlyWaitTimeNBR = NULL"]
    Diff --> End["End: Initial Only Wait Time Output
                  <br>0 allowed if exactly at CheckIn =  Rooming 
                  NULL if not Initial Encounter
                  , OR if RoomingDTS &lt; CheckInDTS, 
                  , OR if Rooming &gt;= Chair/Visit End<br>"]
    NullI --> NULL
    NULL L_NULL_End_0@--> End

    B@{ shape: diam}
    I@{ shape: diamond}
    style A fill:#e1f5fe
    style NullW fill:#fff3e0
    style NullI fill:#fff3e0
    style NULL fill:#fff3e0
    style End fill:#e8f5e8
    linkStyle 1 stroke:#4caf50,stroke-width:3px,fill:none
    linkStyle 2 stroke:#f44336,stroke-width:2px,stroke-dasharray:5,5,fill:none
    linkStyle 4 stroke:#f44336,stroke-width:2px,stroke-dasharray:5,5,fill:none
    linkStyle 5 stroke:#4caf50,stroke-width:3px,fill:none
    linkStyle 6 stroke:#4caf50,stroke-width:3px,fill:none
    linkStyle 7 stroke:#f44336,stroke-width:2px,stroke-dasharray:5,5,fill:none
    L_NULL_End_0@{ curve: linear }

    '''