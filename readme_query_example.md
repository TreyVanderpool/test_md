    
#Query Mode Examples:
We'll create a small zFAM instance to store some basic MLB team data in. The examples will show how to insert, update, delete and retrieve from the datastore.

Create a Query Mode zFAM instance with the following field definitions:
- team_id: 10 characters {**PRIMARY KEY**, short name for teams, like 'dodgers'}
- team: 50 character {full team name}
- players: 3 numeric {number of players on the team}
- win_world_series: 1 char {Y/N flag if they won the world series}

1. Add a new team called the "Texas Rangers" using the short name "rangers" to the instance.  
    POST http://zos.com:777/pro/league/teams/?zQL
    Body: INSERT,(FIELDS(team_id=rangers),(team=Texas Rangers),(players=26),(win_world_series=N))  
    Request Headers:
    - None

    HTTP Code: 200  
    HTTP Text: Ok  
    Body: Empty  

2. Add a second team called the "St. Louis Cardinals" using the short name "cards" to the instance.  
    POST http://zos.com:777/pro/league/teams/?zQL  
    Body: INSERT,(FIELDS(team_id=cards),(team=St. Louis Cardinals),(players=26),(win_world_series=N))  
    Request Headers:
    - None

    HTTP Code: 200  
    HTTP Text: Ok  
    Body: Empty  

3. Update the Cardinals to a 40 man roster.  
    PUT http://zos.com:777/pro/league/teams/?zQL  
    Body: UPDATE,(FIELDS(players=40)),WHERE((team_id=cards))  
    Request Headers:
    - None

    HTTP Code: 200  
    HTTP Text: Ok  
    Body: Empty  
    
4. Query the teams to show the values.  
    The data returned in the body is fixed length formatted. All string values will be padded on the right with spaces and numeric values will be padded on the left with zeros.  
    GET http://zos.com:777/pro/league/teams/?zQL,SELECT,(FIELDS(team_id),(team),(players))  
    Body: None  
    Request Headers:
    - None

    HTTP Code: 200  
    HTTP Text: Ok  
    Body: cards&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;St. Louis Cardinals&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;040N  

5. Issue the same query as #4 but request the data to return in JSON format.  
    All string values will be padded on the right with spaces and numeric values will be padded on the left with zeros.  
    GET http://zos.com:777/pro/league/teams/?zQL,SELECT,(FIELDS(team_id),(team),(players))  
    Body: None  
    Request Headers:
    - Content-Type: application/json  

    HTTP Code: 200  
    HTTP Text: Ok  
    Body: [{ "team_id":"cards&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;", "team":"St. Louis Cardinals&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;", "players":040, "win_world_series":"N" }]  

6. Issue the same query as #4 but request more than 1 row be returned.  
    All string values will be padded on the right with spaces and numeric values will be padded on the left with zeros.  
    GET http://zos.com:777/pro/league/teams/?zQL,SELECT,(FIELDS(team_id),(team),(players)),(OPTIONS(ROWS=99))  
    Body: None  
    Request Headers:
    - None  

    HTTP Code: 200  
    HTTP Text: Ok  
    Body: cards&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;St. Louis Cardinals&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;040Nrangers&nbsp;&nbsp;&nbsp;Texas Rangers&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;026N  
