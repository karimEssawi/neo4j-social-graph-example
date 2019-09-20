# neo4j-social-graph-example
A simple social graph neo4j database with cypher query examples


## Run guide
First things first, install [pipenv](https://github.com/pypa/pipenv) to create and configure a virtualenv. Once pipenv is installed, run `pipenv install` to install the required packages.

Next, run `docker compose up` to spin up a [neo4j](https://neo4j.com) container. Once neo4j is up and running, you can navigate to Neo4j Browser on `localhost:7474`.

Now that the database is up an running, you'll need to enter some data. To do so, simply run `pipenv run python create_graph.py`. The python script will create a simple social graph with "Person" Nodes and "KNOWS" relationships.

## Querying with cypher
#### Friends of Friends
Find all of Joe's second-degree friends
```
MATCH 
  (person:Person)-[:KNOWS]-(friend:Person)-[:KNOWS]-
  (foaf:Person)
WHERE 
  person.name = "Joe"
  AND NOT (person)-[:KNOWS]-(foaf)
RETURN
  foaf
```              
Joe knows Sally, and Sally knows Anna. Bob is excluded from the result because, in addition to being a 2nd-degree friend through Sally, he's also a first-degree friend.

#### Common Friends
Find friends in common between Joe and Sally
```
MATCH 
  (user:Person)-[:KNOWS]-(friend)-
    [:KNOWS]-(foaf:Person)
WHERE 
  user.name = "Joe" AND foaf.name = "Sally"
RETURN 
  friend.name AS friend
```
Joe and Sally both know Bob.

#### Connecting Paths
Find all paths, up to 6 degrees, between Joe and Billy
```
MATCH 
  path = shortestPath(
    (p1:Person)-[:KNOWS*..6]-(p2:Person)
  )
WHERE 
  p1.name = "Joe" AND p2.name = "Billy"
RETURN
  path
```
There's only one shortest path between Joe and Billy in this dataset, so that path is returned.


## Further reading
[Neo4j developer guide](https://neo4j.com/developer/get-started/)

[Neo4j Cypher manual](https://neo4j.com/docs/cypher-manual/current/)