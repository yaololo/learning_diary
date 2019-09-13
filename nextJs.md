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
Files should be placed un `static` folder not any other name.
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

TODO -- add more


## Dynamic Routing
Define params in route, 
