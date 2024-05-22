# Transcendence

## Project Infrastructure

```mermaid
flowchart LR
	nginxFront["nginx front service"]
	browser["User browser"]
	subgraph pongLauncher["Game launcher server"]
        gameInstance["Game instance"]
	    gameLauncherService["Game launcher service"]
        gameLauncherNginx["Game service nginx"]
	end
    subgraph history["History server"]
        historyNginx["History service nginx"] -->
	    historyService["History service"] -->
	    matchDB[(Matches DB)]
    end
    subgraph user["User server"]
        userNginx["User service nginx"] -->
        userService["User service"] -->
	    userDB[(Users DB)]
    end
    subgraph stats["Stats server"]
	    statsNginx["Stats service nginx"] -->
	    statsService["Stats service"] -->
	    statsDB[(Users stats DB)]
    end
    subgraph auth["Auth server"]
        authNginx["Auth service nginx"] -->
        authService["Auth service"] -->
        authDB[(Auth DB)]
    end
    subgraph gameProvider
	    gameProviderService["Game provider"]
	    gameProviderNginx["Game provider nginx"]
    end
    browser ----------> nginxFront
    browser ---> gameProviderNginx
    gameProviderService ----> historyNginx & authNginx
    browser ---> statsNginx  & historyNginx & authNginx
    browser -------> userNginx
    browser <--> gameInstance
    gameLauncherNginx --> gameLauncherService --> gameInstance
    gameProviderService <----> gameLauncherNginx
    gameProviderNginx --> gameProviderService
    gameProviderService ----> statsNginx
	  
```

## DB Entities relationship
```mermaid
erDiagram
	USER {
		number id PK "ID of the user"
		string username UK "Username use to log in"
		string display_name "Display name"
		string password "The user password"
		number pong_elo "The pong elo for matchmaking"
		number custom_pong_elo "The custom pong elo for matchmaking"
		number gun_fight_elo "The gun fight elo for matchmaking"
		string picture "The profile picture path"
	}
	
	MATCH {
		number id PK "ID of the match"
		player1_id number FK "ID of the first player"
		player2_id number FK "ID of the second player"
		plater1_score number "Score of the first player"
		plater2_score number "Score of the second player"
		string game "Name of the game"
	}
	
	USER ||--o{ MATCH : has
```
