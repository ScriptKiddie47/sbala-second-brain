
```jsx
import { useState } from 'react';
const colors = ['#FFCA88', '#DE7272'];
function App() {
    const [bgColor, setBgColor] = useState('#FFCA88');
    function handleOnMouseEnter() {
        setBgColor(colors[1]);
    }
    function handleOnMouseLeave() {
        setBgColor(colors[0]);
    }
    return (
        <div className="container">
            <h1>H1</h1>
            <input type="text" placeholder="What's your name?" />
            <button
                onMouseEnter={handleOnMouseEnter}
                onMouseLeave={handleOnMouseLeave}
                style={{ backgroundColor: bgColor }}
            >
                Submit
            </button>
        </div>
    );
}
export default App;
```

1. Not much explanation is required here actually.