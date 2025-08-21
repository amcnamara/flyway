---
subtitle: MongoDB - Native Connectors
---

- **Status:** Preview
- **Verified Versions:** V5,V8
- **Maintainer:** {% include redgate-badge.html %}

## Supported Versions and Support Levels

{% include database-boilerplate.html %}

## Terminology
We have to map Flyway concepts and language rooted in the relational database world to MongoDB - this is how Flyway sees the mapping:

| MongoDB Concept | Flyway Concept  |
| --------------- |-----------------|
| database        | database/schema |
| collection      | table           |
| document        | row             |
| transaction     | transaction     |

## Using Flyway with MongoDB Native Connectors

- If you are using javascript migrations then you'll need [`mongosh`](https://www.mongodb.com/docs/mongodb-shell/install/) to be installed. If you are not using the default database, you will need to include the database name and auth source in your url.
- Any configuration you require needs to go into the connection string (Flyway's URL parameter) - see [Mongo Connection String Options](https://www.mongodb.com/docs/manual/reference/connection-string-options/). This is to enable the same configuration to be applied whether Flyway is talking to the database through the API or via Mongosh.
- There is a [tutorial available here](/tutorials/tutorial---using-native-connectors-to-connect-to-mongodb).
- To use Native Connectors with our official Docker image, you will need to use either the `redgate/flyway:{{site.flywayVersion}}-mongo`, `-alpine-mongo` or `-azure-mongo` images.

## Limitations

- Transactions using `.js` migrations will not work. A warning will be displayed if `executeInTransaction` is set.
See [this blog post](https://documentation.red-gate.com/display/FD/Flyway+Native+Connectors+-+MongoDB) for more details.
