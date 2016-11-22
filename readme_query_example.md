    
#Query Mode Examples:
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
