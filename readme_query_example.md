    
#Query Mode Examples:
We'll create a small zFAM instance to store some basic MLB team data in. The examples will show how to insert, update, delete and retrieve from the datastore.

Create a Query Mode zFAM instance with the following field definitions:
- team_id: 10 characters {PRIMARY KEY, short name for teams, like 'dodgers'}
- team: 50 character {full team name}
- players: 3 numeric {number of players on the team}
- win_world_series: 1 char {Y/N flag if they won the world series}

1. Add a new team to the instance.


    Small zFAM query mode instance that contains the following attributes:
    - org_type: team originization type, "mlb", "nfl", "nba"
    - team_name: name of the team
    - players: number of players on the team
    - salaries: total team salary
    
    List all teams for the "mlb":
    ?zQL,SELECT,(FIELDS(org_type),(team)),(WHERE(org_type=mlb))
    
    List all teams, number of players and team salaries for the "nfl":
    ?zQL,SELECT,(FIELDS(team),(players),(salaries)),(WHERE((org_type=nfl))
    
    List 10 of the "nba" teams:
    ?zQL,SELECT,(FIELDS(team),(players),(salaries)),(OPTIONS(ROWS=10))
