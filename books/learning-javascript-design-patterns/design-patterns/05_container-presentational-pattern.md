# Container/Presentational Pattern

## What Is It?

Enforces separation of concerns by separating the view from the application logic.

1.  **Presentational Components**: Components that care about _**how**_ data is shown to the user. In this example, that's the _rendering the list of dog images_.
2.  **Container Components**: Components that care about _**what**_ data is shown to the user. In this example, that's _fetching the dog images_.

**Presentational Components** receieve their data through props. It does not modify the data. They are _usually_ stateless, unless they need a state for UI purposes.

**Container Components** pass data to presentational components. 

Testing Presentational Components is easy. They are also easy to make reusable.


## Example

### Presentational Component

```js
import React from "react";

export default function DogImages({ dogs }) {
  return dogs.map((dog, i) => <img src={dog} key={i} alt="Dog" />);

}
```

### Container Components

```js
import React from "react";
import DogImages from "./DogImages";

export default class DogImagesContainer extends React.Component {
  constructor() {
    super();
    this.state = {
      dogs: []
    };
  }
  
  componentDidMount() {
    fetch("https://dog.ceo/api/breed/labrador/images/random/6")
      .then(res => res.json())
      .then(({ message }) => this.setState({ dogs: message }));
  }

  render() {
    return <DogImages dogs={this.state.dogs} />;
  }
}
```

### Presentational Component (Hooks Method)
```js
import React from "react";
import useDogImages from "./useDogImages";

export default function DogImages() {
  const dogs = useDogImages();
  return dogs.map((dog, i) => <img src={dog} key={i} alt="Dog" />);
}
```

### Container Component (Hooks Method)
```js
export default function useDogImages() {
  const [dogs, setDogs] = useState([]);

  useEffect(() => {
    fetch("https://dog.ceo/api/breed/labrador/images/random/6")
      .then(res => res.json())
      .then(({ message }) => setDogs(message));
  }, []);

  return dogs;
}
```

## Cons

Hooks make it possible to separate concerns without having to use this pattern. You can also now use state in functional components (no class components needed). This pattern isn't really needed anymore.

Can be overkill in a small application. 