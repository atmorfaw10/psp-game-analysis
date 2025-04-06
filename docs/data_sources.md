# Data sources for PSP vs DS Analysis

## Primary Sources
| Source        | Data Collected                          | Period Covered | Notes                          |
|---------------|----------------------------------------|----------------|--------------------------------|
| Kaggle        | PSP/DS global sales, regional splits   | 2004–2016      | Cross-check with VGChartz      |
| IGDB          | Exclusive titles, genres, developers   | 2004–2014      | Filter for PSP/DS only         |
| Metacritic    | Critic/user scores for exclusives      | 2004–present   | Focus on pre-2014 releases     |
| Wikipedia     | Hardware specs, release dates          | N/A            | Use for tech comparisons       |
| Reddit Pushshift | Player sentiment, complaints         | 2005–present   | Search keywords: "PSP vs DS"   |

## Links
* [Kaggle Video Game Sales](https://www.kaggle.com/datasets/rush4ratio/video-game-sales-with-ratings/data)
* [IGDB API](https://www.igdb.com/api)
* [VGChartz (PSP)](https://www.vgchartz.com/games/games.php?name=&keyword=&console=PSP&region=All&developer=&publisher=&goty_year=&genre=&boxart=Both&banner=Both&ownership=Both&showmultiplat=No&results=50&order=Sales&showtotalsales=0&showtotalsales=1&showpublisher=0&showpublisher=1&showvgchartzscore=0&showvgchartzscore=1&shownasales=0&showdeveloper=0&showcriticscore=0&showcriticscore=1&showpalsales=0&showreleasedate=0&showreleasedate=1&showuserscore=0&showuserscore=1&showjapansales=0&showlastupdate=0&showlastupdate=1&showothersales=0&showshipped=0&showshipped=1)
* [VGChartz (DS)](https://www.vgchartz.com/games/games.php?name=&keyword=&console=DS&region=All&developer=&publisher=&goty_year=&genre=&boxart=Both&banner=Both&ownership=Both&showmultiplat=No&results=50&order=Sales&showtotalsales=0&showtotalsales=1&showpublisher=0&showpublisher=1&showvgchartzscore=0&showvgchartzscore=1&shownasales=0&showdeveloper=0&showcriticscore=0&showcriticscore=1&showpalsales=0&showreleasedate=0&showreleasedate=1&showuserscore=0&showuserscore=1&showjapansales=0&showlastupdate=0&showlastupdate=1&showothersales=0&showshipped=0&showshipped=1)
* [PSP Models and Specs](https://en.wikipedia.org/wiki/PlayStation_Portable)
* [DS](https://en.wikipedia.org/wiki/Nintendo_DS)
* [PSP Metacritic](https://www.metacritic.com/browse/game/psp/)
* [DS Metacritic](https://www.metacritic.com/browse/game/ds/)


## Validation Notes
- **Discrepencies**: VGChartz sales estimates may differ from Kaggle. Prioritize Kaggle for consistency- **Gaps**:
	-  Missing Japan-only DS games? consider supplementing with Japanese sources if needed
	-  Missing PSN data: PSN data is unavailable


## Database Schema (PostgreSQL)
```mermaid
erDiagram
    GAMES ||--o{ SALES : "1-to-many"
    GAMES ||--o{ RATINGS : "1-to-many"
    GAMES {
	varchar(20) game_id PK
	text title
	varchar(3) platform "PSP|DS"
	date release_date
	varchar(50) genre
	text developer
    }
    SALES {
	serial sale_id PK
	varchar(20) game_id FK
	numeric(5,2) na_sales "millions"
	numeric(5,2) eu_sales
	numeric(5,2) jp_sales
	integer year
    }
    RATINGS {
	serial rating_id PK
	varchar(20) game_id FK
	numeric(3,1) metacritic_score
	numeric(2,1) user_score
    }
    HARDWARE {
	varchar(3) platform PK "PSP|DS"
	numeric(5,2) launch_price_usd
	integer battery_life_hours
	varchar(10) media_type "UMD|Cartridge"
    }
```

**Key Constraints**
* `platform` enforced as PSP or DS
* Sales values stores as `numeric` for precise calculations
* `serial` PKs for auto-incrementing IDs
