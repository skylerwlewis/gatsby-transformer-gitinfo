# gatsby-transformer-gitinfo

Add some git information on `File` fields from latest commit: date, author and email.

## Install

`npm install --save @skylerwlewis/gatsby-transformer-gitinfo`

**Note:** You also need to have `gatsby-source-filesystem` installed and configured so it
points to your files.

## How to use

In your `gatsby-config.js`

```javascript
module.exports = {
  plugins: [
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        path: `./src/data/`,
      },
    },
    `@skylerwlewis/gatsby-transformer-gitinfo`,
  ],
}
```

Where the _source folder_ `./src/data/` is a git versioned directory.

The plugin will add several fields to `File` nodes: `gitLogLatestAuthorName`, `gitLogLatestAuthorEmail` and `gitLogLatestDate`. These fields are related to the latest commit touching that file.

If the file is not versioned, these fields will be `null`.

They are exposed in your graphql schema which you can query:

```graphql
query {
  allFile {
    edges {
      node {
        fields {
          gitLogLatestAuthorName
          gitLogLatestAuthorEmail
          gitLogLatestDate
        }
        internal {
          type
          mediaType
          description
          owner
        }
      }
    }
  }
}
```

Now you have a `File` node to work with:

```json
{
  "data": {
    "allFile": {
      "edges": [
        {
          "node": {
            "fields": {
              "gitLogLatestAuthorName":"John Doe",
              "gitLogLatestAuthorEmail": "john.doe@github.com",
              "gitLogLatestDate": "2019-10-14T12:58:39.000Z"
            },
            "internal": {
              "contentDigest": "c1644b03f380bc5508456ce91faf0c08",
              "type": "File",
              "mediaType": "text/yaml",
              "description": "File \"src/data/example.yml\"",
              "owner": "gatsby-source-filesystem"
            }
          }
        }
      ]
    }
  }
}
```

## Configuration options

**`include`** [regex][optional]

This plugin will try to match the absolute path of the file with the `include` regex.
If it *does not* match, the file will be skipped.

```javascript
module.exports = {
  plugins: [
    {
      resolve: `@skylerwlewis/gatsby-transformer-gitinfo`,
      options: {
        include: /\.md$/i, // Only .md files
      },
    },
  ],
}
```


**`ignore`** [regex][optional]

This plugin will try to match the absolute path of the file with the `ignore` regex.
If it *does* match, the file will be skipped.

```javascript
module.exports = {
  plugins: [
    {
      resolve: `@skylerwlewis/gatsby-transformer-gitinfo`,
      options: {
        ignore: /\.jpeg$/i, // All files except .jpeg
      },
    },
  ],
}
```

**`dir`** [string][optional]

The root of the git repository. Will use current directory if not provided.

## Example

**Note:** the execution order is first `ìnclude`, then `ignore`.

```javascript
module.exports = {
  plugins: [
    {
      resolve: `@skylerwlewis/gatsby-transformer-gitinfo`,
      options: {
        include: /\.md$/i,
        ignore: /README/i,  // Will match all .md files, except README.md
      },
    },
  ],
}
```
