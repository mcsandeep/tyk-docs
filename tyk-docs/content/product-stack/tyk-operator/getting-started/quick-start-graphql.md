---
date: 2017-03-24T16:39:31Z
title: Sample GraphQL Proxy
tags: ["Tyk Operator", "Sample", "Kubernetes"]
description: "Tyk Operator manifest example"
---

```yaml
apiVersion: tyk.tyk.io/v1alpha1
kind: ApiDefinition
metadata:
  name: trevorblades
spec:
  name: trevorblades
  use_keyless: true
  protocol: http
  active: true
  proxy:
    target_url: https://countries.trevorblades.com
    listen_path: /trevorblades
    strip_listen_path: true
  graphql:
    enabled: true
    version: "2"
    execution_mode: proxyOnly
    schema: |
      directive @cacheControl(maxAge: Int, scope: CacheControlScope) on FIELD_DEFINITION | OBJECT | INTERFACE

      enum CacheControlScope {
        PUBLIC
        PRIVATE
      }

      type Continent {
        code: ID!
        name: String!
        countries: [Country!]!
      }

      input ContinentFilterInput {
        code: StringQueryOperatorInput
      }

      type Country {
        code: ID!
        name: String!
        native: String!
        phone: String!
        continent: Continent!
        capital: String
        currency: String
        languages: [Language!]!
        emoji: String!
        emojiU: String!
        states: [State!]!
      }

      input CountryFilterInput {
        code: StringQueryOperatorInput
        currency: StringQueryOperatorInput
        continent: StringQueryOperatorInput
      }

      type Language {
        code: ID!
        name: String
        native: String
        rtl: Boolean!
      }

      input LanguageFilterInput {
        code: StringQueryOperatorInput
      }

      type Query {
        continents(filter: ContinentFilterInput): [Continent!]!
        continent(code: ID!): Continent
        countries(filter: CountryFilterInput): [Country!]!
        country(code: ID!): Country
        languages(filter: LanguageFilterInput): [Language!]!
        language(code: ID!): Language
      }

      type State {
        code: String
        name: String!
        country: Country!
      }

      input StringQueryOperatorInput {
        eq: String
        ne: String
        in: [String]
        nin: [String]
        regex: String
        glob: String
      }

      """The `Upload` scalar type represents a file upload."""
      scalar Upload
    playground:
      enabled: true
      path: /playground
```