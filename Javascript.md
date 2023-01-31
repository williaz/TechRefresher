
### DOM
- add event listener
```js
  <h1 id="header">Hello World</h1>
  <button id="button">Change Header</button>
  <script>
    const header = document.getElementById("header");
    const button = document.getElementById("button");

    button.addEventListener("click", function() {
      header.innerHTML = "The header has been changed";
    });
  </script>

```
### React Testing
```js
import React from 'react';
import { render, cleanup, fireEvent } from '@testing-library/react';
import { shallow } from 'enzyme';

import MyComponent from './MyComponent';

afterEach(cleanup);

describe('MyComponent', () => {
  it('renders correctly with Jest and Enzyme', () => {
    const wrapper = shallow(<MyComponent />);
    expect(wrapper).toMatchSnapshot();
  });

  it('renders correctly with React Testing Library', () => {
    const { getByText } = render(<MyComponent />);
    const linkElement = getByText(/click me/i);
    expect(linkElement).toBeInTheDocument();
  });

  it('calls the click handler when the button is clicked', () => {
    const mockHandler = jest.fn();
    const { getByText } = render(<MyComponent onClick={mockHandler} />);
    const button = getByText(/click me/i);
    fireEvent.click(button);
    expect(mockHandler).toHaveBeenCalled();
  });
});
```
