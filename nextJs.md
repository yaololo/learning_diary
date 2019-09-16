# Next

## Routing

In Next.js routing is based on file structure. Files under `page` structure will rendered according to different route.

```jsx
// under./page/index, and visit localhost, this page will be served
function Home() {
  return <div>Welcome to Next.js!</div>;
}

export default Home;
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

Files should be placed under `static` folder not anything else.

```jsx
function MyImage() {
  return <img src="/static/my-image.png" alt="my image" />;
}

export default MyImage;
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

> Note: Pages that are statically optimized by automatic prerendering will be hydrated without their route parameters provided (query will be empty, i.e. {}). After hydration, Next.js will trigger an update to your application to provide the route parameters in the query object. If your application cannot tolerate this behavior, you can opt-out of static optimization by capturing the query parameter in getInitialProps.

## Populating `<head>`

Next expose a built-in component for appending elements to the `<head>` of the page. To avoid duplicate tags in your <head> you can use the key property, which will make sure the tag is only rendered once: (below example will only render second `<head>`)

```jsx
import Head from "next/head";

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
  );
}

export default IndexPage;
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

_**Note: SSR will only render the page when `getInitialProps` get resolved**_

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

Redirect page according to file structure and there is no routes defined in the application.

```jsx
/**
 * Basic example
 * pages/index.js
 */
import Link from "next/link";

function Home() {
  return (
    <>
      <ul>
        <li>Home</li>
        <li>
          <Link href="/about">
            <a>About Us</a>
          </Link>
        </li>
      </ul>

      <h1>This is our homepage.</h1>
    </>
  );
}

export default Home;
```

Note: if passing a functional component as a child of `<Link>` you will need to wrap it in React.forwardRef

```jsx
/**
 * Example with React.forwardRef
 */
import React from "react";
import Link from "next/link";

// `onClick`, `href`, and `ref` need to be passed to the DOM element
// for proper handling
const MyButton = React.forwardRef(({ onClick, href }, ref) => (
  <a href={href} onClick={onClick} ref={ref}>
    Click Me
  </a>
));

export default () => (
  <>
    <Link href="/another">
      <MyButton />
    </Link>
  </>
);
```

###### Custom Route

- Consider you have the URL /post/:slug.

- You created pages/post.js:

```jsx
import { useRouter } from "next/router";

const Post = () => {
  const router = useRouter();
  const { slug } = router.query;

  return <p>My Blog Post: {slug}</p>;
};

export default Post;
```

- You add the route to express (or any other server) on `server.js` file (this is only for SSR). This will route the url /post/:slug to pages/post.js and provide slug as part of the query object to the page.

```jsx
server.get("/post/:slug", (req, res) => {
  return app.render(req, res, "/post", { slug: req.params.slug });
});

// From client side
// `as` is used by server
<Link href="/post?slug=something" as="/post/something">
```

###### With URL Object & Replace instead of push url

```jsx
// pages/index.js
import Link from "next/link";

function Home() {
  return (
    <div>
      Click{" "}
      <Link href={{ pathname: "/about", query: { name: "Zeit" } }} replace>
        <a>here</a>
      </Link>{" "}
      to read more
    </div>
  );
}

export default Home;

/*
 ** redirect to /about?name=Zeit, the replace keyword in Link will prevent adding a new entry.
 */
```

###### Use Component that support 'onClick' event & Forcing the Link to expose href to its child

`<Link>` supports any component that supports the `onClick` event. In case you don't provide an `<a>` tag, it will only add the `onClick` event handler and won't pass the href property.

```jsx
// pages/index.js
import Link from "next/link";

function Home() {
  return (
    <div>
      Click{" "}
      <Link href="/about">
        <img src="/static/image.png" alt="image" />
      </Link>
    </div>
  );
}

export default Home;

/*
 ** In the code snippet, missing <a> tag will not generate a hyperlink. Although the element
 ** is clickable, there will not be any HTML node with href attribute tell user or Google bot
 ** that it is a Hyperlink and redirecting user to other page.
 */

// To force to expose Link href attribute to child component, use `passHref` field.
// This will help with search engine crawling the page and better for SEO

/* Usage
<Link href="/about" passHref>
  <img src="/static/image.png" alt="image" />
</Link>
*/
```

###### Disabling the scroll changes to top on page

Use `scroll={false}` to stop the default behaviour of `<Link>` is to scroll to the top of the page. When there is a hash defined it will scroll to the specific id, just like a normal `<a>` tag.

`<Link scroll={false} href="/?counter=10"><a>Disables scrolling</a></Link>`

###### Imperatively
