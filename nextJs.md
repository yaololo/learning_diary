# Next
## Routing
In Next.js routing is based on file structure.  Files in under `page` structure will rendered according to different route.
```jsx
// under./page/index, and visit localhost, this page will be served
function Home() {
  return <div>Welcome to Next.js!</div>
}

export default Home
```

## CSS
Support
- Styled components
- Styletron
- Glamor
- Cxs
- Aphrodite
- Fela

## Static file serving
Files should be placed un `static` folder not anything else.
```jsx
function MyImage() {
  return <img src="/static/my-image.png" alt="my image" />
}

export default MyImage
```
## Automatic Pre-rendering

Next.js automatically determines that a page is static (can be pre-rendered) if it has no blocking data requirements. This determination is made by the absence of `getInitialProps` in the page.
> `getInitialProps` is a function which prepares initial props for the components.

Next.js will statically optimize your page automatically by pre-rendering it to static HTML if `getInitialProps` is absent. This allow Next to emit hybrid applications that contain both server-rendered and statically generated pages, which is fast by default as some page is already rendered into html and ready to be served. (Advantage against CSR which have to wait the entire application to be ready)

TODO -- add more information


## Dynamic Routing
Define params in route, `pages/post/[pid].js` will generate a route with `pid` as params. e.g `/post/abc` -> this route will have `query` params `{ pid: 'abc' }`. `/post/abc?foo=bar` -> `{ foo: 'bar', pid: 'abc' }`.

Multiple dynamic route works the same way `pages/post/[pid]/[comment].js` -> `/post/1/a-comment` -> `{ pid: '1', comment: 'a-comment' }`

Use `Link`
```jsx
<Link href="/post/[pid]" as="/post/abc">
  <a>First Post</a>
</Link>
```
- `href` is the path inside `page` directory
- `as` the path generated in browser url bar

 _**NOTE: Given `pages/post/[pid].js` having `/post/abc?pid=bcd` in url will generate query params object `{ pid: 'abc' }`. This is because query and route param name are the same**_

 > Note: Predefined routes take precedence over dynamic routes. For example, if you have `pages/post/[pid].js` and `pages/post/create.js`, the route `/post/create` will be matched by `pages/post/create.js` instead of the dynamic route ([pid]). `post/sss` -> `post/[pid]`

>  Note: Pages that are statically optimized by automatic prerendering will be hydrated without their route parameters provided (query will be empty, i.e. {}). After hydration, Next.js will trigger an update to your application to provide the route parameters in the query object. If your application cannot tolerate this behavior, you can opt-out of static optimization by capturing the query parameter in getInitialProps.

## Populating `<head>`
Next expose a built-in component for appending elements to the `<head>` of the page. To avoid duplicate tags in your <head> you can use the key property, which will make sure the tag is only rendered once: (below example will only render second `<head>`)
```jsx
import Head from 'next/head'

function IndexPage() {
  return (
    <div>
      <Head>
        <title>My page title</title>
        <meta
          name="viewport"
          content="initial-scale=1.0, width=device-width"
          key="viewport"
        />
      </Head>
      <Head>
        <meta
          name="viewport"
          content="initial-scale=1.2, width=device-width"
          key="viewport"
        />
      </Head>
      <p>Hello world!</p>
    </div>
  )
}

export default IndexPage
```
_**Note: content within `<head>` will be destroyed upon `unmounting` the component.**_

## Fetching Data and Component Lifecycle
Either use hooks or `initial data population` to fetch data. Data returned from `getInitialProps` must be in Object format.
```jsx
// Function style
import fetch from "isomorphic-unfetch";

const Page = props => {
  return <div>Next stars: {props.stars}</div>;
};

Page.getInitialProps = async ({ req }) => {
  const res = await fetch("https://api.github.com/repos/zeit/next.js");
  const json = await res.json();
  return { stars: json.stargazers_count };
};

export default Page;


// Class style

import React from 'react'

class HelloUA extends React.Component {
  static async getInitialProps({ req }) {
    const userAgent = req ? req.headers['user-agent'] : navigator.userAgent
    return { userAgent }
  }

  render() {
    return <div>Hello World {this.props.userAgent}</div>
  }
}

export default HelloUA
```
React hook is still client side fetching data whereas `getInitialProps` is server side loading. Hook works according to component lifecycle, `getInitialProps` is only triggered when client navigate to a different route.

> - `getInitialProps` can not be used in children components. Only in pages.
> - If you are using some server only modules inside `getInitialProps`, make sure to import them properly, otherwise, it'll slow down your app.

Server side rendering `getInitialProps` will receive additional HTTP request property
- pathname - path section of URL
- query - query string section of URL parsed as an object
- asPath - String of the actual path (including the query) shows in the browser
- req - HTTP request object (server only)
- res - HTTP response object (server only)
- err - Error object if any error is encountered during the rendering

## Routing
