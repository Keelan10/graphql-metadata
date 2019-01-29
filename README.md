# graphql-annotations
Annotate a GraphQL schema

<p align="center">
  <a href="https://www.patreon.com/akryum" target="_blank">
    <img src="https://c5.patreon.com/external/logo/become_a_patron_button.png" alt="Become a Patreon">
  </a>
</p>

## Sponsors

### Silver

<p align="center">
  <a href="https://vueschool.io/" target="_blank">
    <img src="https://vueschool.io/img/logo/vueschool_logo_multicolor.svg" alt="VueSchool logo" width="200px">
  </a>
</p>

### Bronze

<p align="center">
  <a href="https://vuetifyjs.com" target="_blank" title="Vuetify">
    <img src="https://cdn.discordapp.com/attachments/537832759985700914/537832771691872267/Horizontal_Logo_-_Dark.png" width="100">
  </a>
</p>

## Installation

```bash
npm i graphql-annotations
```

## Usage

### Annotations parsing

Here is a very basic example with a `namespace` (here `'db'`) and a `description` that needs to be parsed:

```js
const { parseAnnotations } = require('graphql-annotations')

const result = parseAnnotations('db', `
  This is a description
  @db.length: 200
  @db.foo: 'bar'
  @db.unique
  @db.index: { name: 'foo', type: 'string' }
`)
```

This will output an object containing the annotations:

```js
{
  length: 200,
  foo: 'bar',
  unique: true,
  index: { name: 'foo', type: 'string' }
}
```

In a GraphQL schema, you can use the `description` property on `GraphQLObjectType`, `GraphQLField`...

```js
const { parseAnnotations } = require('graphql-annotations')
const { buildSchema, isObjectType } = require('graphql')

const schema = buildSchema(`
  """
  @db.table: 'users'
  """
  type User {
    """
    @db.primary
    """
    id: ID!
  }
`)

const typeMap = schema.getTypeMap()
for (const key in typeMap) {
  const type = typeMap[key]
  // Tables
  if (isObjectType(type)) {
    const typeAnnotations = parseAnnotations('db', type.description)
    console.log(type.name, typeAnnotations)
    const fields = type.getFields()
    for (const key in fields) {
      const field = fields[key]
      const fieldAnnotations = parseAnnotations('db', field.description)
      console.log(field.name, fieldAnnotations)
    }
  }
}
```

Which will output:

```js
User { table: 'users' }
id { primary: true }
```
