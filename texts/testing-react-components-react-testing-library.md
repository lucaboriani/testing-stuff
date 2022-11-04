# Testing React Components with React Testing Library

see [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/)

repo [here](https://github.com/testing-library/react-testing-library)

React Testing Library builds on top of [DOM Testing Library](https://testing-library.com/) by adding APIs for working with React components.

Projects created with Create React App have out of the box support for React Testing Library. If that is not the case, you can add it via npm like so:

`npm install --save-dev @testing-library/react`

or 

`yarn add --dev @testing-library/react`

## A bit of examples 

from [TDD in reactjs with React testing library (RTL) and Jest](https://dev.to/sakethkowtha/testing-with-rtl-and-jest-31c9)

- RTL is like a wrapper for DOM Testing library with reactjs support
- It is light weight over other react testing libraries
- It provides good utility functions those will help us to write tests in best practices
- Good querying functions.

Some examples for using RTL

### Testing basic stateless react component

```js
import { render, screen } from "@testing-library/react";

const MyApp = () => {
  return <div>Hello world</div>;
};

test("MyApp should render hello world", () => {
  render(<MyApp />);
  expect(screen.getByText("Hello world")).toBeInTheDocument();
});
```

Testing component with props

```js
import { render, screen } from "@testing-library/react";

const MyApp = ({ message }) => {
  return <div>{message}</div>;
};

test("MyApp should render hello world", () => {
  render(<MyApp message={"Hello world"} />);
  expect(screen.getByText("Hello world")).toBeInTheDocument();
});
```

### Testing components which has hooks

```js
import { render, screen } from "@testing-library/react";

const MyApp = () => {
  let { id } = useParams();
  return <div>{id}</div>;
};

test("MyApp should render hello world", () => {
  jest.mock("react-router-dom", () => ({
    ...jest.requireActual("react-router-dom"),
    useParams: jest.fn().mockReturnValue({ id: "123" }),
  }));
  render(<MyApp />);
  expect(screen.getByText("123")).toBeInTheDocument();
});
```


### Testing components which has API call

```js
import {getArticles} from './services'
import {render, screen} from '@testing-library/react'

const MyApp = () => {
    const [articles, setArticles] = useState([])
    useEffect(() => {
        const response = await getArticles()
        setArticles(response)
    }, [])
    return <div>
            {
                articles.map(article => <div>{article}</div>)
            }
        </div>
}

test("MyApp should render hello world", () => {
    jest.mock('./services', () => ({
        getArticles: jest.fn()
    }));
    render(<MyApp />)
    expect(getArticles).toBeCalled()
})
```
### Testing components which has API call and loader

```js
import {getArticles} from './services'
import {render, screen} from '@testing-library/react'

const MyApp = () => {
    const [articles, setArticles] = useState([])
    const [showLoader, setShowLoader] = useState(false)

  useEffect(() => {
      setShowLoader(true)
      const response = await getArticles()
      setShowLoader(false)
      setArticles(response)
  }, [])
    if(showLoader) return <Loader data-testId="loader" />
    return <div>
            {
                articles.map((article, index) => <div key={index}>{article}</div>)
            }
        </div>
}

test("MyApp should render hello world", async () => {
    const mockResponse = ["Article 1", "Article 2"]
    jest.mock('./services', () => ({
    getArticles: jest.fn().mockResolveValue(mockResponse)
    }));
    render(<MyApp />)
    const loader = screen.queryByTestId("loader")
  expect(loader).toBeInTheDocument()
    await waitForElementToBeRemoved(() => loader)
    expect(screen.getAllByText("Article").length).toBe(2)
})
```

### Testing user events

```js
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

const MyApp = () => {
  const [counter, setCounter] = useState(0);
  return (
    <div>
      <button data-testId="inc-btn" onClick={() => setCounter(counter + 1)}>
        +
      </button>
      <span data-testId="value">{counter}</span>
      <button data-testId="dec-btn" onclick={() => setCounter(counter - 1)}>
        -
      </button>
    </div>
  );
};

test("MyApp should render hello world", () => {
  render(<MyApp />);
  const counterValue = screen.getByTestId("value");
  expect(counterValue).toBe("0");
  userEvent.click(screen.getByTestId("inc-btn"));
  expect(counterValue).toBe("1");
  userEvent.click(screen.getByTestId("dec-btn"));
  expect(counterValue).toBe("0");
});
```

### Testing custom hooks

Take a look at [How to test custom React hooks](https://kentcdodds.com/blog/how-to-test-custom-react-hooks) by Kent C. Dodds as it's a pretty complex issue and the article's great!


