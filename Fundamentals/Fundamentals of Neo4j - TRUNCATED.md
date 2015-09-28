Fundamentals of Neo4j
========================================================
author: Nicole White
date: 
width: 1400
height: 1000



Setup
========================================================

- Go to `neo4j.com`
- Click on Download Neo4j
- Download the Community Edition

### Mac

- Unzip the `.tar.gz` file (double-click it)
- `cd` to the extracted directory in the Terminal
- Execute `./bin/neo4j start`
- Go to `localhost:7474` in your browser

### Windows

- Double-click the downloaded `.exe` file
- Run Neo4j after installation finishes
- Click Start
- Go to `localhost:7474` in your browser

Agenda
========================================================

- Cypher: Syntax Basics
- Cypher: Match & Return
- Cypher: Constraints & Indexes
- Cypher: LOAD CSV

Today's Data
========================================================

### `github.com/nicolewhite/neo4j-presentations`

- Navigate to `Fundamentals`
- Copy the contents of `import.txt` into the Neo4j Browser and run

Today's Slides
========================================================

### `nicolewhite.github.io/neo4j-presentations/fundamentals`

Section Header
========================================================
title: false

# Cypher: Syntax Basics

Drawing Graph Patterns 
========================================================

### Nodes

`()`

### Relationships

`[]`

### Paths

`()-[]-()`

`()-[]->()`

`()<-[]-()`

Identifiers, Node Labels, & Relationship Types
========================================================

### Query

```
MATCH (t:Terminal) 
RETURN t
```

### Notes
- `t` is an identifier
- `Terminal` is a node label

Identifiers, Node Labels, & Relationship Types
========================================================

### Query

```
MATCH (x:Terminal) 
RETURN x
```

### Notes
- Identifiers can be whatever you want
- This query and the query from the previous slide are identical in terms of output

Identifiers, Node Labels, & Relationship Types
========================================================

### Query

```
MATCH (g:Gate)-[r:IN_TERMINAL]->(t:Terminal) 
RETURN g, r, t
```

### Notes
- `g`, `r`, and `t` are identifiers
- `Gate` and `Terminal` are node labels
- `IN_TERMINAL` is a relationship type

Identifiers, Node Labels, & Relationship Types
========================================================

### Query

```
MATCH p = (:Gate)-[:IN_TERMINAL]->(:Terminal)
RETURN p
```

### Notes
- `p` is an identifier
- `Gate` and `Terminal` are node labels
- `IN_TERMINAL` is a relationship type
- The identifiers `g`, `r`, and `t` are no longer necessary; the entire path has been assigned to the identifier `p`
- This query and the query from the previous slide are identical in terms of output

Graph versus Tabular Results
========================================================

### Graph Result

#### Query

```
MATCH (t:Terminal) 
RETURN t
```

#### Notes
- Returning the identifier `t` returns the node entity
- This query will return a graph in the Neo4j Browser

### Tabular Result

#### Query

```
MATCH (t:Terminal) 
RETURN t.name
```

#### Notes
- Properties are accessed with `{identifier}.{property}`
- This query will return a table in the Neo4j Browser

Section Header
========================================================
title: false

# Cypher: Match & Return

Aliasing in the RETURN Clause
========================================================

**Category names.**



### Query

```
MATCH (c:Category)
RETURN c.name AS category
```

### Results

```
           category
   American Cuisine
              Asian
                Bar
           Barbecue
             Coffee
  Desserts & Snacks
          Fast Food
      Italian/Pizza
  Mexican/Southwest
      Sandwich/Deli
            Seafood
     Power Charging
 Grand Hyatt Dining
```

The WHERE Clause
========================================================

**Names of barbecue places.**



### Query

```
MATCH (p:Place)-[:IN_CATEGORY]->(c:Category)
WHERE c.name = 'Barbecue'
RETURN p.name AS barbecue
```

### Results

```
                barbecue
 The Salt Lick Bar-B-Que
            Railhead BBQ
   Dickey's Barbecue Pit
        Cousin's Bar-B-Q
```

Shorthand WHERE Conditioning
========================================================

**Names of barbecue places.**



### Query

```
MATCH (p:Place)-[:IN_CATEGORY]->(:Category {name:'Barbecue'})
RETURN p.name AS barbecue
```

### Result

```
                barbecue
 The Salt Lick Bar-B-Que
            Railhead BBQ
   Dickey's Barbecue Pit
        Cousin's Bar-B-Q
```

Aggregation
========================================================

**Categories and the count of how many places are in that category.**



### Query

```
MATCH (:Place)-[:IN_CATEGORY]->(c:Category)
RETURN c.name AS category, COUNT(*) AS places
```

### Result

```
           category places
  Desserts & Snacks     10
  Mexican/Southwest      9
     Power Charging      3
             Coffee      3
 Grand Hyatt Dining      3
   American Cuisine     11
              Asian      3
           Barbecue      4
      Italian/Pizza      3
                Bar      7
      Sandwich/Deli      5
          Fast Food      7
            Seafood      3
```

The ORDER BY and LIMIT Clauses
========================================================

**Top five categories ordered descending by the count of how many places are in that category.**



### Query

```
MATCH (:Place)-[:IN_CATEGORY]->(c:Category)
RETURN c.name AS category, COUNT(*) AS places
ORDER BY places DESC
LIMIT 5
```

### Result

```
          category places
  American Cuisine     11
 Desserts & Snacks     10
 Mexican/Southwest      9
               Bar      7
         Fast Food      7
```

Lab
========================================================

**List the bars in alphabetical order.**

Lab
========================================================

**List the bars in alphabetical order.**



### Query

```
MATCH (p:Place)-[:IN_CATEGORY]->(c:Category)
WHERE c.name = 'Bar'
RETURN p.name AS place
ORDER BY place
```

### Result

```
                            place
                      Cowtown Bar
                          Fly Bar
           Gas Monkey Bar & Grill
                Rider's World Bar
 Texas Stadium Skybox Bar & Grill
     Tigin Irish Pub & Restaurant
                        Vino Volo
```

Multiple Patterns in the MATCH Clause
========================================================

**The gate number of where the bars are located.**



### Query

```
MATCH (p:Place)-[:IN_CATEGORY]->(c:Category),
      (p)-[:AT_GATE]->(g:Gate)
WHERE c.name = 'Bar'
RETURN p.name AS place, g.number AS gate
```

### Result

```
                            place gate
                          Fly Bar   27
                Rider's World Bar   15
                      Cowtown Bar   35
                        Vino Volo   17
                        Vino Volo   27
     Tigin Irish Pub & Restaurant   20
           Gas Monkey Bar & Grill   31
 Texas Stadium Skybox Bar & Grill   25
 Texas Stadium Skybox Bar & Grill    6
```

Multiple Patterns in the MATCH Clause
========================================================

**The gate number and terminal of where the bars are located.**



### Query

```
MATCH (p:Place)-[:IN_CATEGORY]->(c:Category),
      (p)-[:AT_GATE]->(g:Gate),
      (g)-[:IN_TERMINAL]->(t:Terminal)
WHERE c.name = 'Bar'
RETURN p.name AS place, g.number AS gate, t.name AS terminal
ORDER BY place
```

### Result

```
                            place gate terminal
                      Cowtown Bar   35        A
                          Fly Bar   27        E
           Gas Monkey Bar & Grill   31        D
                Rider's World Bar   15        E
 Texas Stadium Skybox Bar & Grill   25        C
 Texas Stadium Skybox Bar & Grill    6        B
     Tigin Irish Pub & Restaurant   20        D
                        Vino Volo   17        A
                        Vino Volo   27        E
```

Multiple Patterns in the MATCH Clause
========================================================

**The closest coffee place to gate 10 in terminal B.**



### Query

```
MATCH (p:Place)-[:IN_CATEGORY]->(c:Category),
      (p)-[:AT_GATE]->(g:Gate),
      (g)-[:IN_TERMINAL]->(t:Terminal)
WHERE c.name = 'Coffee' AND t.name = 'B'
RETURN p.name AS place, 
       ABS(g.number - 10) AS distance, 
       g.number AS gate, 
       t.name AS terminal
ORDER BY distance
```

### Result

```
            place distance gate terminal
 Starbucks Coffee        2    8        B
    Dunkin Donuts        3    7        B
 Starbucks Coffee        5   15        B
 Starbucks Coffee       18   28        B
```

Lab
========================================================

**Find all of the Dunkin Donuts locations. Return the terminal and gate.**

Lab
========================================================

**Find all of the Dunkin Donuts locations. Return the terminal and gate.**



### Query

```
MATCH (donuts:Place {name:'Dunkin Donuts'}),
      (donuts)-[:AT_GATE]->(g:Gate),
      (g)-[:IN_TERMINAL]->(t:Terminal)
RETURN t.name AS terminal, g.number AS gate
ORDER BY terminal, gate
```

### Result

```
 terminal gate
        A   13
        A   28
        B    7
        D   22
        E    6
```

Negating Patterns in the WHERE Clause
========================================================

**Two users who have both liked Blimpie but are not friends.**



### Query

```
MATCH (u1:User)-[:LIKED]->(p:Place)<-[:LIKED]-(u2:User)
WHERE p.name = 'Blimpie' AND NOT (u1)-[:FRIENDS_WITH]-(u2)
RETURN u1, u2, p
```

Lab
========================================================

**Find places that Alice has liked but not checked into.**

Lab
========================================================

**Find places that Alice has liked but not checked into.**



### Query

```
MATCH (alice:User)-[:LIKED]->(p:Place)
WHERE alice.name = 'Alice' AND NOT (alice)-[:CHECKED_INTO]->(p)
RETURN p.name AS place
```

### Result

```
           place
     Burger King
 T.G.I. Friday's
 Cool River Cafe
      Manchu Wok
```

The OPTIONAL MATCH Clause
========================================================

**Users and how many friends they have. If using MATCH, only users who have at least one friend are returned.**



### Query

```
MATCH (u:User)-[:FRIENDS_WITH]-(f:User)
RETURN u.name AS user, COUNT(f) AS friends
ORDER BY friends DESC
```

### Result

```
    user friends
    Hank       3
   Alice       3
  Elaine       2
 Forrest       2
   David       1
 Charles       1
```

The OPTIONAL MATCH Clause
========================================================

**Users and how many friends they have. If using OPTIONAL MATCH, all users are returned, including users who have no friends.**



### Query

```
MATCH (u:User)
OPTIONAL MATCH (u)-[:FRIENDS_WITH]-(f:User)
RETURN u.name AS user, COUNT(f) AS friends
ORDER BY friends DESC
```

### Result

```
    user friends
    Hank       3
   Alice       3
  Elaine       2
 Forrest       2
   David       1
 Charles       1
     Bob       0
     Jan       0
   Greta       0
     Ian       0
```

The COLLECT Function
========================================================

**Users and how many friends they have, along with the names of those friends.**



### Query

```
MATCH (u:User)
OPTIONAL MATCH (u)-[:FRIENDS_WITH]-(f:User)
RETURN u.name AS user, 
       COUNT(f) AS friends, 
       COLLECT(f.name) AS friends_names
ORDER BY friends DESC
```

### Result

```
    user friends          friends_names
    Hank       3   Elaine, Alice, David
   Alice       3 Forrest, Charles, Hank
  Elaine       2          Hank, Forrest
 Forrest       2          Elaine, Alice
   David       1                   Hank
 Charles       1                  Alice
     Bob       0                       
     Jan       0                       
   Greta       0                       
     Ian       0                       
```

The WITH Clause
========================================================

**Users and how many friends they have, along with the names of those friends. Return only users with 2 or more friends.**



### Query

```
MATCH (u:User)-[:FRIENDS_WITH]-(f:User)
WITH u.name AS user, 
     COUNT(f) AS friends, 
     COLLECT(f.name) AS friends_names
WHERE friends >= 2
RETURN user, friends, friends_names
ORDER BY friends DESC
```

### Result

```
    user friends          friends_names
    Hank       3   Alice, David, Elaine
   Alice       3 Charles, Forrest, Hank
  Elaine       2          Forrest, Hank
 Forrest       2          Alice, Elaine
```

The WITH Clause
========================================================

**Users and how many friends they have, along with the names of those friends.**
**Return only users with 2 or more friends and who are friends with Hank.**



### Query

```
MATCH (u:User)-[:FRIENDS_WITH]-(f:User)
WITH u.name AS user, 
     COUNT(f) AS friends, 
     COLLECT(f.name) AS friends_names
WHERE friends >= 2 AND 'Hank' IN friends_names
RETURN user, friends, friends_names
ORDER BY friends DESC
```

### Result

```
   user friends          friends_names
  Alice       3 Charles, Forrest, Hank
 Elaine       2          Forrest, Hank
```

OR with Relationship Types
========================================================

**Fast food places that Alice has either liked or checked into.**



### Query

```
MATCH (alice:User)-[r:LIKED|CHECKED_INTO]->(place:Place),
      (place)-[:IN_CATEGORY]->(c:Category)
WHERE alice.name = 'Alice' AND c.name = 'Fast Food'
RETURN place.name AS place, TYPE(r) AS type
```

### Result

```
             place         type
       Burger King        LIKED
        McDonald's CHECKED_INTO
 Pizza Hut Express        LIKED
 Pizza Hut Express CHECKED_INTO
```

Relationship Properties
========================================================

**The places that Alice has liked and the score she gave the place.**



### Query

```
MATCH (alice:User)-[r:LIKED]->(p:Place)
WHERE alice.name = 'Alice'
RETURN p.name AS place, r.score AS score
ORDER BY score DESC
```

### Result

```
             place score
 Pizza Hut Express     4
   Cool River Cafe     4
        Manchu Wok     4
   T.G.I. Friday's     2
       Burger King     1
```

Recommendations
========================================================

**Fast food or dessert & snack places that Alice's friends have liked ordered by the average score her friends gave the place.**
**Alice can't have already liked or checked into the place.**



### Query

```
MATCH (alice:User)-[:FRIENDS_WITH]-(friend:User),
      (friend)-[r:LIKED]->(place:Place),
      (place)-[:IN_CATEGORY]->(c:Category)
WHERE alice.name = 'Alice' AND 
      c.name IN ['Fast Food', 'Desserts & Snacks'] AND
      NOT (alice)-[:LIKED|CHECKED_INTO]->(place)
RETURN place.name AS place, AVG(r.score) AS avg_score
ORDER BY avg_score DESC
```

### Result

```
                     place avg_score
      Grove Natural Snacks       7.0
             Smoothie King       6.5
               UFood Grill       6.0
                   Wendy's       2.0
 Popeyes Louisiana Kitchen       1.0
```

Lab
========================================================

**Find all places that have received a score of 10 from any user. Return the place's name and the user's name.**

Lab
========================================================

**Find all places that have received a score of 10 from any user. Return the place's name and the user's name.**



### Query

```
MATCH (u:User)-[r:LIKED]->(p:Place)
WHERE r.score = 10
RETURN u.name AS user, p.name AS place
```

### Result

```
    user                         place
   David               Boars Head Deli
 Forrest    Pappadeaux Seafood Kitchen
 Forrest              Power & Internet
     Ian                     Cereality
     Jan Freshens Smoothies and Yogurt
     Jan                   Jamba Juice
```

Dealing with NULL Values
========================================================

**Fast food places that Alice has either liked or checked into and the score she gave them, if any.**



### Query

```
MATCH (alice:User)-[r:LIKED|CHECKED_INTO]->(place:Place),
      (place)-[:IN_CATEGORY]->(c:Category)
WHERE alice.name = 'Alice' AND c.name = 'Fast Food'
RETURN place.name AS place, r.score AS score
```

### Result

```
             place score
       Burger King     1
        McDonald's  null
 Pizza Hut Express     4
 Pizza Hut Express  null
```

Dealing with NULL Values
========================================================

**Fast food places that Alice has either liked or checked into and the score she gave them, if any.**



### Query

```
MATCH (alice:User)-[r:LIKED|CHECKED_INTO]->(place:Place),
      (place)-[:IN_CATEGORY]->(c:Category)
WHERE alice.name = 'Alice' AND c.name = 'Fast Food'
RETURN place.name AS place, 
       COALESCE(r.score, 'No Score') AS score
```

### Result

```
             place    score
       Burger King        1
        McDonald's No Score
 Pizza Hut Express        4
 Pizza Hut Express No Score
```

Dealing with NULL Values
========================================================

**Fast food places that Alice has either liked or checked into and the score she gave them, if any.**



### Query

```
MATCH (alice:User)-[r:LIKED|CHECKED_INTO]->(place:Place),
      (place)-[:IN_CATEGORY]->(c:Category)
WHERE alice.name = 'Alice' AND c.name = 'Fast Food'
RETURN place.name AS place, 
       TYPE(r) AS action, 
       COALESCE(r.score, 'No Score') AS score
```

### Result

```
             place       action    score
       Burger King        LIKED        1
        McDonald's CHECKED_INTO No Score
 Pizza Hut Express        LIKED        4
 Pizza Hut Express CHECKED_INTO No Score
```

Paths
========================================================

**Who do Charles and David know each other through?**



### Query

```
MATCH p = (a:User)-[:FRIENDS_WITH*]-(b:User)
WHERE a.name = 'Charles' AND b.name = 'David'
RETURN p
```



Paths
========================================================

**Who do Charles and David know each other through?**



### Query

```
MATCH p = (a:User)-[:FRIENDS_WITH*]-(b:User)
WHERE a.name = 'Charles' AND b.name = 'David'
RETURN LENGTH(p) AS path_length
```

### Result

```
 path_length
           3
           5
```

Paths
========================================================

**Who do Charles and David know each other through? Return the name property from all the nodes on the path.**



### Query

```
MATCH p = (a:User)-[:FRIENDS_WITH*]-(b:User)
WHERE a.name = 'Charles' AND b.name = 'David'
RETURN EXTRACT(x IN NODES(p) | x.name) AS nodes,
       LENGTH(p) AS path_length
```

### Result

```
                                        nodes path_length
                  Charles, Alice, Hank, David           3
 Charles, Alice, Forrest, Elaine, Hank, David           5
```

Paths
========================================================

**Who do Charles and David know each other through? Search for an exact path length of 3.**



### Query

```
MATCH p = (a:User)-[:FRIENDS_WITH*3]-(b:User)
WHERE a.name = 'Charles' AND b.name = 'David'
RETURN EXTRACT(x IN NODES(p) | x.name) AS nodes, 
       LENGTH(p) AS path_length
```

### Result

```
                       nodes path_length
 Charles, Alice, Hank, David           3
```

Paths
========================================================

**Who do Charles and David know each other through? Search for paths up to length-five.**



### Query

```
MATCH p = (a:User)-[:FRIENDS_WITH*..5]-(b:User)
WHERE a.name = 'Charles' AND b.name = 'David'
RETURN EXTRACT(x IN NODES(p) | x.name) AS nodes,
       LENGTH(p) AS path_length
```

### Result

```
                                        nodes path_length
                  Charles, Alice, Hank, David           3
 Charles, Alice, Forrest, Elaine, Hank, David           5
```

Collection Predicates
========================================================

**Who do Charles and David know each other through? Search for paths up to length-five. Forrest must be on the path.**



### Query

```
MATCH p = (a:User)-[:FRIENDS_WITH*..5]-(b:User)
WHERE a.name = 'Charles' AND 
      b.name = 'David' AND
      ANY(x IN NODES(p) WHERE x.name = 'Forrest')
RETURN EXTRACT(x IN NODES(p) | x.name) AS nodes,
       LENGTH(p) AS path_length
```

### Result

```
                                        nodes path_length
 Charles, Alice, Forrest, Elaine, Hank, David           5
```

Collection Predicates
========================================================

**Who do Charles and David know each other through? Search for paths up to length-five. No one on the path can have liked Fly Bar.**



### Query

```
MATCH p = (a:User)-[:FRIENDS_WITH*..5]-(b:User)
WHERE a.name = 'Charles' AND 
      b.name = 'David' AND
      NONE(x IN NODES(p) 
           WHERE (x)-[:LIKED]->(:Place {name:'Fly Bar'})
      )
RETURN EXTRACT(x IN NODES(p) | x.name) AS nodes,
       LENGTH(p) AS path_length
```

### Result

```
                       nodes path_length
 Charles, Alice, Hank, David           3
```

Shortest Path
========================================================

**What's the shortest path between Greta and Wendy's?**



### Query

```
MATCH p = shortestPath((greta:User)-[*]-(wendy:Place))
WHERE greta.name = 'Greta' AND wendy.name = 'Wendy\'s'
RETURN p
```

Shortest Path
========================================================

**What's the shortest path between Greta and Wendy's through likes?**



### Query

```
MATCH p = shortestPath((greta:User)-[:LIKED*]-(wendy:Place))
WHERE greta.name = 'Greta' AND wendy.name = 'Wendy\'s'
RETURN p
```

All Shortest Paths
========================================================

**Find all shortest paths between Greta and Wendy's through likes.**



### Query

```
MATCH p = allShortestPaths((greta:User)-[:LIKED*]-(wendy:Place))
WHERE greta.name = 'Greta' AND wendy.name = 'Wendy\'s'
RETURN p
```

Lab
========================================================
**Find all shortest paths between Greta and Wendy's through likes. Grove Natural Snacks can't be on the path.**

Lab
========================================================
**Find all shortest paths between Greta and Wendy's through likes. Grove Natural Snacks can't be on the path.**



### Query

```
MATCH p = allShortestPaths((greta:User)-[:LIKED*]-(wendy:Place))
WHERE greta.name = 'Greta' AND 
      wendy.name = 'Wendy\'s' AND
      NONE(x IN NODES(p)
		   WHERE x.name = 'Grove Natural Snacks')
RETURN p
```

Section Header
========================================================
title: false

# Cypher: Constraints & Indexes

View Constraints and Indexes Already Online
========================================================

Enter `:schema` into the Neo4j Browser

Add a Uniqueness Constraint
========================================================



### Query

```
CREATE CONSTRAINT ON (u:User) ASSERT u.name IS UNIQUE
```

### Notes
- Adding a uniqueness constraint also creates an index on the node label, node property pair
- Check this with `:schema` in the Neo4j Browser
- Adding a uniqueness constraint to already-existing data that violates the uniqueness constraint results in an error

Adding Data that Violates a Uniqueness Constraint
========================================================



### Query

```
CREATE (:User {name:'Your Name'})
```

### Notes
- If you followed along and already created a User node with your name, this will result in an error
- Use the `MERGE` clause instead

Add an Index
========================================================



### Query

```
CREATE INDEX ON :Gate(number)
```

### Notes
- There are multiple gates with the same gate number; a uniqueness constraint is not appropriate

Section Header
========================================================
title: false

# Cypher: LOAD CSV



Setup
========================================================

### Clear Your DB



```
MATCH (n) OPTIONAL MATCH (n)-[r]-() DELETE n, r
```

### Filepaths

#### OSX and Unix 
`file:///path/to/data.csv`

#### Windows
`file:C:/path/to/data.csv`

Uniqueness Constraints & Indexes
========================================================




```
CREATE CONSTRAINT ON (t:Terminal) ASSERT t.name IS UNIQUE;
```

```
CREATE CONSTRAINT ON (p:Place) ASSERT p.name IS UNIQUE;
```

```
CREATE CONSTRAINT ON (u:User) ASSERT u.name IS UNIQUE;
```

```
CREATE INDEX ON :Gate(number);
```

Gates & Terminals
========================================================

`gates_terminals.csv`



### Query



























```
Error in paste0("file://", WORKING_DIRECTORY, "csv/gates_terminals.csv") : 
  object 'WORKING_DIRECTORY' not found
```
