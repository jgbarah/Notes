# My methodology for producing Kibana dashboards

Kibana is very flexible, and allows for many ways for combining indexes, searches, visualizations... Thiese are just some rules I'm using to keep the production of dashboards consistent, with minor surprises to whom may have to deal with them, while at the same time trying to keep them simple.

The main aspects covered are:

* Indexes and types (ElasticSearch)
* Index aliases (ElasticSearch)
* Index patterns (Kibana)
* Searches (Kibana)
* Visualizations (Kibana)
* Dashboards (Kibana)

## Indexes and types

For now, I'm using an index for each kind of document (JSON document) to store. I'm still wandering if I should be using types, for for now I'm skipping them.

For example, an index may be `commits`.

For maintenance, if an index has to be reconstructed while still maintaining the "old" one untouched (for example, for producing a fresh version), version numbers or dates can be used. For example, `commmits-1`, `commits-2`, `commits-3`, or `commmits-2016-03-30`, `commmits-2016-04-22`, `commits-2016-05-12`. In combination with index aliases, the index Kibana is uses can be pointed to the appropriate "real" index easily (see below).

## Index aliases

For each index, I'm using several ElasticSearch aliases, one for each time_field name I'm expecting to use.

In Kibana, when defining an index pattern (see below), you may select a time_field. That will be the field which will be used by time filters in the dashboard. In many cases, a given kind of JSON document may have several time_fields (potentially, each date field can be a time:field). Since index patterns cannot have a name, but their name comes directly from the name of the underlying alias that they map, we need an "index name" for each time_field. Since we cannot have several index names, we use index aliases. From the point of view of Kibana, they are pretty much the same as indexes.

For example, for the `commmits` index, we could have `commmits_author`, and `commits_commmitter`, since for each commmit we have two dates: the authoring and the committing dates. Note the underscore instead of the minus sign, to avoid confusion with indexes versions, for which I use the latter.

Index aliases can be produced as follows:

```
curl -XPOST 'http://localhost:9200/_aliases' -d '
{
    "actions" : [
        { "add" : { "index" : "commits", "alias" : "commits_author" } }
        { "add" : { "index" : "commits", "alias" : "commits_committer" } }
    ]
}'
```

As I mentioned above, aliases can be used as well to produce a new index while the "old" one is still in use, and then switch to the new one as soon as it is ready. For example, while having in production `index-1`, some error is detected, and a new `index-2`, with the same data, but the error fixed, is produced. Once `index-2` is ready, I can switch the index aliases to the new index, minimizing the time without having the index alias available for Kibana (in fact, the documentation says the operation is atomic):

```
curl -XPOST 'http://localhost:9200/_aliases' -d '
{
    "actions" : [
        { "remove" : { "index" : "commits-1", "alias" : "commits_author" } },
        { "add" : { "index" : "commits-2", "alias" : "commits_author" } }
        { "remove" : { "index" : "commits-1", "alias" : "commits_committer" } }
        { "add" : { "index" : "commits-2", "alias" : "commits_committer" } }
    ]
}'
```

*Important note:* if needed, an index alias may refer to more than one index.

## Index patterns

In Kibana, the first step is to define an index pattern (Settings | Indices).

For each ElasticSearch index alias, I produce a new index pattern, with the corresponding date field as time_field. These index patterns will be the basis for any visualization in the dashboard.

## Searches

Searches in Kibana are producec in the "Discover" menu entry.

Although in Kibana visualizations can be bases on an ElasticSearch index (or index alias, for that matter), I always base each visualization in a Search. The main reasons are uniformity, and ease to use:

* Simplicity: In some cases I need Searches (mainly when a search needs to include a filter, see below), so it is better to always have a Search. That way you don't have to longer when they are based on searches or indices: they are always based on searches in my schema. Besides, if at first sight I don´t include a filter, but later I realize I need one, I can easily modify the search to include it, and all visualizations remain exactly the same.

* Ease to use: Searrches are easily available through a menu. Once they are built, when visuaizations are constructed it is just a matter of selecting them from the searches menu. Besides, a search includes only certain fields, which in the case of indexes with many fields, simplifies the view.

Usually, for each index pattern I define at least one Search, with the fields I intend to use from it. But if needed, several searches, maybe with different filters and different fields, can be produced.

For naming searches, I try to use the index pattern name (which is the index alias in ElasticSearch). If there are several searches for the same index pattern, one of them (the most simple) is as that, while for the next ones I include after it some text related to the fields selected or filters included.

When the search has a clear semantics (meaning) I use it as the name. For instance, for GitHub issues I have an index, `issues`. But issues can be "real issues" or pull requests. For each of them, I use a search. But since they have clear semantics, I call them `issues` and `pull_requests` (instead of `issues-issues` and `issues-pull_requests`). However, maybe I should revisit this to make names more predictible.

## Visualizations

Each visualization is based on a search. I try to keep the name of the visualization simple, and related to its semantics. It is important too to rename the parameters in the charts to something meaningful, instead to using the default names.

## Dashboards

I try to have simple, semantic names for each visualization.
