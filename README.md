# Meteor Neo4j

This package will allow you to use the open-source graph database Neo4j with your Meteor project. If you are mapping relationships, this database will be much more efficient than the built-in Mongo database. You can run it both locally and with [GrapheneDB](http://www.graphenedb.com/) for production.

## Installation and setup

You will have to install Neo4j globally on your machine. It is also important to note that you must start up Neo4j independently of Meteor every time you want to use it using the command `neo4j start`. Install Neo4j with the following command:

    brew install neo4j
    npm install -g neo4j

Then start Neo4j and start Meteor:

    neo4j start
    meteor add ccorcos:neo4j
    meteor

## How to use

You should be able to see the Neo4j browser at the Neo4j url (default: http://localhost:7474/). Neo4j provides you with a pretty nice user interface to play around with and test out your cypher queries.

When you're done, be sure to stop Neo4j as well, otherwise it will run in the background forever:

    neo4j stop

Sometimes it hangs and won't stop. Try `ps aux | grep neo4j` and then `kill -9 <pid>`.

To instatiate a connection, on the server, use:

    Neo4j = new Neo4jDB()

Also note, that Neo4j does not accept nested property lists, so its best to structure your Mongo collections similarly.

If you intend to use GrapheneDB, first you must set up an account through their website and obtain your url, username, and password. Then, you have two options for initializing GrapheneDB: passing a url or setting the GRAPHENEDB_URL environment variable. We will go over both options.

## Connecting with GrapheneDB

Your first option is to pass a url when you instantiate a connection. The format is as follows:

    Neo4j = new Neo4jDB("http://<USERNAME>:<PASSWORD>@projectname.sb05.stations.graphenedb.com:24789")

GrapheneDB provides you with the username, password and the url. Your best bet is to put your username and password into a `settings.json` so you don't expose your username and password, but that's your call.

Your other option is to instantiate the connection as you normally would, but set GRAPHENEDB_URL on startup:

    Meteor.startup ->
      process.env.GRAPHENEDB_URL = "http://<USERNAME>:<PASSWORD>@projectname.sb05.stations.graphenedb.com:24789"

The Neo4j constructor will select the `url` over everything else, so if there are conflicts, it will default to the url you pass. It only runs on `localhost:7474` if it has no other options.

## Querying

Neo4j uses a querying language called `Cypher`, which is pretty similar to SQL. They have a lot of [documentation](http://neo4j.com/docs/stable/cypher-query-lang.html) that is probably worth reviewing. This package uses a very simple implementation of Cypher, which simply passes a string with your query.

    result = Neo4j.query "MATCH (a) RETURN a"

You can also write multi-line queries, which are generally easier to read:

    result = Neo4j.query """
               MATCH (a)
               RETURN a
               """
Do note that you cannot pass strings with a single quote `'`. You must escape single quotes before sending a query. This function might be helpful: `<string>.replace(/'/g, "\\'")`

## Reseting

If you need to clear the entire database, you have two options: send the proper query, or use the `Neo4j.reset()` method.

The query to reset your database is:

    MATCH (n) OPTIONAL MATCH (n)-[r]-() DELETE n,r

Or, you can simply call `Neo4j.reset()`, which will make exactly the same call on the server. Both options will result in the same outcome.

## Counting

The fastest way to count how many results are returned from a particular query is to use the method `Neo4j.count()` and pass in your query. For example, to find out how many nodes and edges you have in your database, you could do something like this:

    Neo4j.count("MATCH (n) OPTIONAL MATCH (n)-[r]-() RETURN n,r")

This will return a number that corresponds to the number of nodes and edges in your database.

## Examples

*Coming soon!*
