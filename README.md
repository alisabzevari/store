# ReaStore

[![Build Status](https://dev.azure.com/alisabzevari/reastore/_apis/build/status/alisabzevari.reastore?branchName=master)](https://dev.azure.com/alisabzevari/reastore/_build/latest?definitionId=2&branchName=master)

This is a proof of concept for an extremely simple and powerful state management library for React. It is inspired by state management mechanism of React components.

## How it works

The library consists of three constructs:

1. **`Store`**: `Store` is a base class that supposed to handle the state management code. The api is almost like React components' state management (a `setState` method and a `state` property that holds the state). You can subclass stores from `Store`, define methods which change the state and assume that your components will react based on changes on state.

```js
export class GithubReposStore extends Store<GithubReposState> {
  constructor() {
    super();

    this.state = {
      username: "alisabzevari",
      repos: [],
      isLoading: false
    };

    this.fetchRepos = this.fetchRepos.bind(this);
  }

  fetchRepos(username) {
    this.setState({ isLoading: true, username });
    fetch(`https://api.github.com/users/${username}/repos`)
      .then(response => response.json())
      .then(result => {
        this.setState({ repos: result, isLoading: false });
      });
  }
}
```

2. **`connect`**: It is like `connect` function in Redux. It creates smart components from dump ones. Simply write a `mapStoresToProps` function accepting stores and returning props of the component:

```js
import { connect } from "reastore";

class GithubRepos extends React.Component {
  ...
}

const mapStoresToProps = ({ githubReposStore, sessionStore }) => (
  {
    username: githubReposStore.state.username,
    isLoading: githubReposStore.state.isLoading,
    repos: githubReposStore.state.repos,
    fetchRepos: githubReposStore.fetchRepos,
    changeCurrentUser: sessionStore.changeCurrentUser
  }
);

export default connect(mapStoresToProps, GithubRepos);
```

3. **`Provider`**: Which is a very simple dependency container. Just Use this component on your application level and assign all stores to its `value` property.

```js
import { Provider } from "reastore";
import { allStores } from "./stores";

const App = () => (
  <Provider value={allStores}>
    <GithubRepos />
  </Provider>
);
```

You can look at the [example directory](https://github.com/alisabzevari/reastore/tree/master/example) for a complete working example.
