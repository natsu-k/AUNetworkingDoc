# AUNetworkingDoc
A simple graphical view of Among Us networking Server to client direction

## The straightforward ones
```mermaid
flowchart 
subgraph InnerNetClient
INCOMR[OnMessageReceived] -->INCHM[HandleMessage]
INCHM --> HM{byte MessageReader.Tag}
HM -->|0| OnGameCreated
HM -->|7| OGJ[OnGameJoined]
HM -->|12| OWFH[OnWaitForHost]
HM -->|16| OGGL[OnGetGameList]
HM -->|17| OPR[OnReportedPlayer]
HM -->|14/20| RET[Return]
HM -->|9/15/18/19/21| BT[Bad Tag]
end
```

## The slightly more interesting ones
```mermaid
flowchart LR
subgraph InnerNetClient
    direction LR
    INCHM[HandleMessage]
    INCHM --> HM{byte MessageReader.Tag}
    HM -->|1| OnPlayerJoined -->|amHost| AmongUsClient.CreatePlayer --> Spawn
    HM -->|2| CoStartGame --- CSG
    subgraph CSG[CoStartGame]
    CoStartGameHost --> Sp[Spawn] --> RMISR[RoleManager.Instance.SelectRoles] 
    RMISR[RoleManager.Instance.SelectRoles] --> SSIB[ShipStatus.Insance.Begin] --> SendClientReady
    CoStartGameClient --> SendClientReady --> SOD["SendOrDisconnect
    MessageReader Tag: 5
    GameData Tag: 7"]
    end
    HM -->|4| OBH[OnBecomeHost?] --> RP[RemovePlayer]
    HM -->|5/6| HandleGameData
    HM -->|8| EGRC[EndGameResult.Create] --> OGE[OnGameEnd]
    HM -->|10| IGP[IsGamePublic] ---|MessageReader boolean| T[True] & F[False]
    HM -->|3| ED[EnqueueDisconnect]
    HM -->|11| ED[EnqueueDisconnect]
    HM -->|13| SE[SetEndpoint] --> C[Connect]
HandleGameData --> HGDI[HandleGameDataInner] --> GameData{{"GameData
&nbsp;
see exploded
graph"}}
  end
```

### GameData - InnerNetClient.HandleMessage Reader.Tag = 5 or 6
```mermaid
flowchart LR
subgraph GameData
    direction TB
HandleGameData --> HGDI[HandleGameDataInner] --> GD
  subgraph GD[GameData]
    direction LR
    HandleGameDataInner{byte MessageReader.Tag}
    HandleGameDataInner --> |1|Data[InnerNetObject.Deserialize] --> Deserialize
    HandleGameDataInner --> |2|handleRPC[HandleRPC] --> RPC
    HandleGameDataInner --> |3|BadTag[Bad Tag]
    HandleGameDataInner --> |4|SpawnHGD[CoHandleSpawn]
    HandleGameDataInner --> |5|Despawn[RemoveNetObject] --> Destroy
    HandleGameDataInner --> |6|SceneChange[CoOnPlayerChangedScene]
    HandleGameDataInner --> |7|Ready[Clientdata.IsReady = true]
    HandleGameDataInner --> |207|hgdspl[Default - 207]
    HandleGameDataInner --> |*|any[Default]
  end
  end
  subgraph Deserialize
  direction TB
    CustomNetworkTransform.Deserialize
    ~~~ GameData.Deserialize 
    ~~~ GameManager.Deserialize
    ~~~ LobbyBehaviour.Deserialize
    ~~~ MeetingHud.Deserialize
    ~~~ PlayerControl.Deserialize
    ~~~ ShipStatus.Deserialize
    ~~~ VoteBanSystem.Deserialize
  end
  subgraph RPC
  CustomNetworkTransform.HandleRPC
    ~~~ GameData.HandleRPC
    ~~~ GameManager.HandleRPC
    ~~~ LobbyBehaviour.HandleRPC
    ~~~ MeetingHud.HandleRPC
    ~~~ PlayerControl.HandleRPC
    ~~~ ShipStatus.HandleRPC
    ~~~ VoteBanSystem.HandleRPC
  end
```
