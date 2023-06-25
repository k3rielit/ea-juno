# Juno GraphQL API

- Endpoint: [service-aggregation-layer.juno.ea.com/graphql](https://service-aggregation-layer.juno.ea.com/graphql)
- [Introspection](https://stackoverflow.com/questions/37397886/get-graphql-whole-schema-query): Disabled

| Operation | Query | Response |
|-----------|-------|----------|
| GameOffers | [GameOffers.graphql](queries/GameOffers.graphql) | [GameOffers.example.json](queries/GameOffers.example.json) |
| gameProductsOffers | [gameProductsOffers.graphql](queries/gameProductsOffers.graphql) | [gameProductsOffers.example.json](queries/gameProductsOffers.example.json) |
| PlanSelection | [PlanSelection.graphql](queries/PlanSelection.graphql) | [PlanSelection.example.json](queries/PlanSelection.example.json) |
| gameRegionalRatingV2 | TODO | TODO |
| Search | TODO | TODO |
| ... | ... | ... |

## Main Fields

| Field | Type | Arguments | Query | Example |
|-------|------|-----------|-------|---------|
| game | Game | slug:string, masterTitleId:string | [GameOffers](queries/GameOffers.graphql) | [GameOffers](queries/GameOffers.example.json) |
| games | GameOffsetPage | slugs:string[] |  [GameOffers](queries/GameOffers.graphql) | [GameOffers](queries/GameOffers.example.json) |
| gameSubscriptions | GameSubscriptionOffsetPage | | [PlanSelection](queries/PlanSelection.graphql) | [PlanSelection](queries/PlanSelection.example.json) |

## Get Started

- Have a rough idea how GraphQL works:
  - [graphql.org/learn](https://graphql.org/learn/)
  - Important topics: [Fields](https://graphql.org/learn/queries/#fields), [Arguments](https://graphql.org/learn/queries/#arguments), [Fragments](https://graphql.org/learn/queries/#fragments), [Operation names](https://graphql.org/learn/queries/#operation-name), [Aliases](https://graphql.org/learn/queries/#aliases)

1. Get a tool to make querying easier: [Insomnia](https://insomnia.rest/download), [Postman](https://www.postman.com/)
2. Go to the [EA Site](https://www.ea.com/), with DevTools (`F12` / `Ctrl+Shift+I`) open on the Network tab
3. Check **Preserve log**, order the list by **Type**, and look for fetch types
4. On the pages scroll through everything (some queries are delayed)
5. Look through the fetched data in the list, look for ones that start with *graphql?...*, then click on them
6. A sidebar pops up, click on **Payload**, then **decoded**, and note *operationName* and *variables*
7. Go to **Preview**, right click, **Expand recursively**, you'll see the JSON response
8. If it's a *new* operation, in Insomnia/Postman create a new GraphQL query:

```graphql
query operationName {
    field { ... }
    field(variable: "value") { ... }
    field(variables: [ "item1" , "item2" ]) { ... }
    # Recreate the query from the json seen in the Preview tab
}
```

9. If it isn't new, see if there's any *new* fields, and add them to the existing query
10. Make a pull request

## Guessing

If you ran out of fetches, and already turned the respone into a working graphql query, it's time to guess new fields, operation names, variables, etc, using the context and __typenames.

For ex. a *Game* type may have a name, slug, id, title, dlcs, etc. Try these, and based on your luck, you may get hints from errors:

```ps
Field \"genre\" is not defined by type \"GameSearchFilterInput\". Did you mean \"genres\"?

Field \"platforms\" is not defined by type \"GameSearchFilterInput\". Did you mean \"playModes\"?

Field \"range\" is not defined by type \"GameSearchFilterInput\". Did you mean \"ratings\"?

Field \"gameTypeds\" is not defined by type \"GameSearchFilterInput\". Did you mean \"gameTypes\"?

Enum \"SearchGameType\" cannot represent non-enum value: \"xd\".

Cannot query field \"gameSlug\" on type \"Game\". Did you mean \"baseGameSlug\", \"gameBans\", \"gameType\", or \"slug\"?

Cannot query field \"id\" on type \"GameAddonsResult\". Did you mean to use an inline fragment on \"GameV2\", \"GameBundlePack\", \"GameCurrency\", \"GameExpansion\", or \"GameMicrocontent\"?
```

Conditioning an [AI](https://chat.openai.com/chat) to act as the API is another great way to discover new data. Feed it with existing queries and structures, and make it guess.

## Notes

Operation names will accept any valid field. For example you can request the **gameSearch** field in the **PlanSelection** query.

**Insomnia** tends to get stuck if you use an incorrect operation name, and when you correct the name, it still queries with the old bad/wrong one.

**Postman** is really slow, and the UI is way too bloated to use it for large queries. So a combination of the 2 tools is the way to go imo.
