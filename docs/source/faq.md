# FAQ

## How does Gluu's Couchbase persistence layer use scan consistency?

When the Gluu Server executes a search with Couchbase (CB), it checks if there is at least one attribute that requires consistency. If such attribute is present, we execute the query with the request_plus consistency. Additionally, in `gluu-couchbase.properties` there are two properties to enable consistency for all queries:

- Default scan consistency: Possible values are `not_bounded`, `request_plus`, `statement_plus`
- connection.scan-consistency: The only possible value is `not_bounded`

It is not recommended to enable for all queries by default, since it will require CB to execute a search only after updating all indexes. This could significantly slow down oxAuth.

As another consideration, queries that require consistency (in the case described above) are executed by oxAuth twice.

The first time it executes with `scan_consistency=not_bounded`, returning a result when the CB server is not highly loaded. Then, it makes second request with `scan_consistency=request_plus` to force a wait time for the index update. According to the CB team, it can take a maximum of 200 ms.

The Gluu team has tested oxAuth working with two CB nodes and replication on a heavy load. The tests were error-free with the default `gluu-couchbase.properties` settings.
