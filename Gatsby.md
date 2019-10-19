# Gatsby

Notes from doing the [official Gatsby tutorial](https://www.gatsbyjs.org/tutorial/)

The tutorials are divided between fundamentals (tutorial 0 to 3) and intermediate (tutorial 4 to 8)

The fundamentals focus on setting up the environment and making/styling pages with React. The intermediate tutorials introduces graphQL with Gatsby and needs to be done in sequence. If you're already familiar with Node.js, npm, Git, CSS Modules and React, I recommend starting at tutorial 4 after installing Gatsby.

### Tutorial 0 - Set Up Your Development Environment

###### command line, homebrew, Node.js, Git, Gatsby CLI, VS Code

#### Install Gatsby CLI:

`npm install -g gatsby-cli`

#### Create New Gatsby Project:

`gatsby new [SITE_DIRECTORY_NAME] [URL_OF_STARTER_GITHUB_REPO]`

For example:

`gatsby new hello-world https://github.com/gatsbyjs/gatsby-starter-hello-world.`

makes a new Gatsby project with root folder hello-world using the gastby-starter-hello-world repo (called "starter")

- Omitting repo url will use default Gatsby starter

#### Starting Gatsby Development Server

`gatsby develop`

### Tutorial 1 - Get to Know Gatsby Building Blocks

###### React, making and linking pages in Gatsby, deploying with Surge

#### Making Pages:

Any React component defined in src/pages/\*.js will automatically become a page. (e.g. src/pages/about.js becomes http://localhost:8000/about.js)

#### Linking between pages:

Gatsby comes with a Link component for relative links

`import { Link } from "gatsby"`

`<Link to="/contact/">Contact</Link>`

#### Deploying Gatsby site:

To build a static site before deploying:

`Gatsby build`

Gatsby will make the build in the /public folder

Then use a static web publishing service such as [Surge](https://surge.sh/)

#### Using Surge:

`npm install --global surge`
then
`surge login`

`Surge public`
to deploy with Surge

###### (after you see this:)

```
project: public

domain: savory-grass.surge.shdomain

```

###### (press enter to continue)

### Tutorial 2 - Introduction to Styling in Gatsby

###### Global styles, CSS Modules, CSS in JS

#### Applying Global CSS:

You can apply global css to entire project by including your .css file in gatsby-browser.js. i.e.

```
import  "./src/styles/global.css"

// or:

// require('./src/styles/global.css')
```

- gatsby-browser.js
  - A file that you will have to create under the root of your project, Gatsby will check this file for any APIs implemented.
  - see [Gatsby Browser APIs](https://www.gatsbyjs.org/docs/browser-apis/)

Note: Gatsby default starter actually uses [layout component](https://www.gatsbyjs.org/docs/global-css/) instead

#### Applying Component-scoped CSS:

Gatsby works out of the box with CSS Modules.
create a CSS Module file (i.e - container.module.css)
then include it in the react component
`import containerStyles from "./container.module.css"`
class name can then be accessed like an object
`<div className={containerStyles.container}>`

#### Using CSS in JS and others

You can enable CSS in JS or other styling methods using Gatsby [plugins](https://www.gatsbyjs.org/docs/css-in-js/)

### Tutorial 3 - Creating Nested Layout Components

###### Gatsby plugins and layout components

#### Gatsby Plugins

You can install [plugins for Gatsby](https://www.gatsbyjs.org/plugins/)
This tutorial uses [typography.js](https://www.gatsbyjs.org/packages/gatsby-plugin-typography/)

#### Layout components

Essentially a React wrapper component that apply its styles to its children components:

```
import React from "react"

export default ({ children }) => (
  <div style={{ margin: `3rem auto`, maxWidth: 650, padding: `0 1rem` }}>
    {children}
  </div>
)
```

### Tutorial 4 - Data in Gatsby

###### GraphQL

You can use Gatsby [without GraphQL](https://www.gatsbyjs.org/docs/using-gatsby-without-graphql/)
Which is recommended for small, simple websites, but as website gets more complicated GraphQL is recommended

#### Storing site metadata

- You can store common site data in gatsby-config.js in the root of the project:

```
module.exports = {
 siteMetadata: {
   title: `Title from siteMetadata`,
 },
 plugins: [
   `gatsby-plugin-emotion`,
   {
     resolve: `gatsby-plugin-typography`,
     options: {
       pathToConfigModule: `src/utils/typography`,
     },
   },
 ],
}
```

&nbsp;&nbsp; site data is stored under siteMetadata in this example.

#### Making a page query:

- Page query is only available for page components (i.e. react components under `src/pages`.
- At the top of the page component file: `import { graphql } from "gatsby"`
- At the end of file, export the query:

```
export const query = graphql`
  query {
    site {
      siteMetadata {
        title
      }
    }
  }
`
```

- Within the react component, data will now be available as `data.site.siteMetadata.title`

```
export default ({ data }) => (
  <Layout>
    <h1>About {data.site.siteMetadata.title}</h1>
    <p>
      We're the only site running on your computer dedicated to showing the best
      photos and videos of pandas eating lots of food.
    </p>
  </Layout>
)
```

#### Using StaticQuery

[StaticQuery](https://www.gatsbyjs.org/docs/static-query/) allows non-page components to retrieve data via GraphQL queries

- at the top of the non-page component file, include useStaticQuery from gatsby
  `import { useStaticQuery, Link, graphql } from "gatsby"`
- **within** the react component, add the query:

```
  const data = useStaticQuery(
    graphql`
      query {
        site {
          siteMetadata {
            title
          }
        }
      }
    `
  )
```

- the data will then be available as `data.site.siteMetadata.title` within the component

### Tutorial 5 - Source Plugins and Rendering Queried Data

#### GraphiQL

GraphiQL is the GraphQL integrated development environment (IDE) that can be accessed at http://localhost:8000/___graphql when the development server is running.

You can use graphiQL to build/test your graphQL queries

#### Source plugins

Source plugins fetch data from their source (i.e. APIs, databases, CMSs, local files, etc.)

This tutorial uses [filesystem source plugin ](https://www.gatsbyjs.org/packages/gatsby-source-filesystem/)to fetch data from file system.

### Tutorial 6 - Transformer plugins

Transformer plugins transform raw content brought in to Gatsby by source plugins.

- This tutorial uses [gatsby-transformer-remark](https://www.gatsbyjs.org/packages/gatsby-transformer-remark/)
  to parse markdown files

You can sort the results of your graphQL query by including arguments in the query. e.g.:

from:

```
export const query = graphql`
  query {
    allMarkdownRemark {
      totalCount
      edges {
        node {
          id
          frontmatter {
            title
            date(formatString: "DD MMMM, YYYY")
          }
          excerpt
        }
      }
    }
  }
`
```

to :

```
export const query = graphql`
  query {
    allMarkdownRemark(sort: { fields: [frontmatter___date], order: DESC }) {
      totalCount
      edges {
        node {
          id
          frontmatter {
            title
            date(formatString: "DD MMMM, YYYY")
          }
          excerpt
        }
      }
    }
  }
`
```

This sorts the query by the date in descending order.

### Tutorial 7 - Programmatically create pages from data

Gatsby can map GraphQL query results to pages automatically at build time.
This involves two steps: Generate the path/slug for the page, and Create the page.

#### Using Gatsby API

You will need to use Gatsby API `onCreateNode` to create slugs and `createPages` to create the page

To use the API, make a file named `gatsby-node.js` in the project root directory and export the API in this file.

#### Creating slugs for pages

- Note: If your data source (i.e. CMS) already provides a slug, you don't need to create the slugs yourself
- Use `onCreateNode` API by exporting it from `gatsby-node.js`:

```
const { createFilePath } = require(`gatsby-source-filesystem`)
exports.onCreateNode = ({ node, getNode, actions }) => {
  const { createNodeField } = actions
  if (node.internal.type === `MarkdownRemark`) {
    const slug = createFilePath({ node, getNode, basePath: `pages` })
    createNodeField({
      node,
      name: `slug`,
      value: slug,
    })
  }
}
```

In this example, every time a node is created or updated, the function checks if the node is a MarkdownRemark, then create the slug using `createFilePath` from `gatsby-source-filesystem`, and then uses onCreateNode's action `createNodeField` to add the new slug field into the node.

#### Creating pages

- Use the `createPages` API in the same way in `gatsby-node.js`:

```
const path = require(`path`)
exports.createPages = async ({ graphql, actions }) => {
  const { createPage } = actions
  const result = await graphql(`
    query {
      allMarkdownRemark {
        edges {
          node {
            fields {
              slug
            }
          }
        }
      }
    }
  `)

  result.data.allMarkdownRemark.edges.forEach(({ node }) => {
    createPage({
      path: node.fields.slug,
      component: path.resolve(`./src/templates/blog-post.js`),
      context: {
        slug: node.fields.slug,
      },
    })
  })
}
```

- In this example, the API waits for the graphql query to resolve first, then uses `createPages` API's `createPage` action to make a page for each node that's part of the result.
- Within `createPage`: `path` is the slug, `component` is the template react component you'll be using to create the page, and in `context` the particular slug to the page is saved here so the template can use graphql query again to fetch the correct data.
- in the template, the graphql query would look similar to:

```
export const query = graphql`
  query($slug: String!) {
    markdownRemark(fields: { slug: { eq: $slug } }) {
      html
      frontmatter {
        title
      }
    }
  }
`
```

- the `$slug` here is from context.

### Tutorial 8 - Preparing a Site to Go Live

###### Audit, add manifest, offline support, and page metadata

#### Running Lighthouse Audit

`gatsby build` and `gatsby serve` to see the production build locally at `localhost:9000`

- Chrome dev tool includes [Lighthouse](https://developers.google.com/web/tools/lighthouse/)
  - In Chrome incognito mode, go to Chrome Dev Tools' audit tab and run lighthouse audit

#### Adding Manifest File

- [`gatsby-plugin-manifest`](https://www.gatsbyjs.org/packages/gatsby-plugin-manifest/) allows you to create a manifest to meet Progressive Web App requirement

#### Adding Offline Support

Part of being PWA requires a service worker runs in the background, deciding to serve network or cached content based on connectivity, allowing for a seamless, managed offline experience.

- [`gatsby-plugin-offline`](https://www.gatsbyjs.org/packages/gatsby-plugin-offline/) creates a service worker

#### Adding Page Metadata

Page Metadata such as title or description helps with SEO.

[React Helmet](https://github.com/nfl/react-helmet) and [`gatsby-plugin-react-helmet`](https://www.gatsbyjs.org/packages/gatsby-plugin-react-helmet/) allows you to create a react component that generate meta data for each auto generated page.
