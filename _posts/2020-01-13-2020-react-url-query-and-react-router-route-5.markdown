---
layout: post
title: "Upgrading To React Router 5.x"
date: 2020-01-14
categories: react, reactjs
---

I recently had to preform a `react-router` upgrade from 4 -> 5.x and ran into a bunch of problems because I was also using [react-url-query](https://github.com/pbeshai/react-url-query/) to upgrade my urls. After the upgrade, the site was broken with the following error:

```
No history provided to react-url-query. Please provide one via configureUrlQuery.
```

After searching around the web, I found the following [issue](https://github.com/pbeshai/react-url-query/issues/37), and eventually, after reading the source code, came to the following solution which seemed to fix my problems:

Before the code looked like this

{% highlight javascript %}
<BrowserRouter>
<RouterToUrlQuery>

<div className="root-container" onClick={this.onRootClick}>
</div>
</RouterToUrlQuery>
</BrowserRouter>
{% endhighlight  %}

Turns out you have to route everything in another `<Route>` and create a new `Context`, passing the `routeProps` to the context provider. The final solution morphed into the following:

```
export const RouterContext = React.createContext({});
```

{% highlight javascript %}
<BrowserRouter>
<Route>
{routeProps => (
<RouterContext.Provider value={routeProps}>
<RouterToUrlQuery routerContext={RouterContext}>

<div className="root-container" onClick={this.onRootClick}>
</div>
</RouterToUrlQuery>
</BrowserRouter>
</Route>
{% endhighlight  %}
