# Core
To bring everything together, we have multiple front-end components that work together to seamlessly work with each input method.

## WPM Testing Page
The Words Per Minute (WPM) testing page is initialized in `wpm_testing.py`. This creates a general user interface frame that the input view and the input method are injected into and displayed.

### Input View
This component displays the text for the user to type and updates as the user inputs letters through the input method. It is initialised with the `full_text` argument, which is the text for the user to type. To interact with it, the input method will call the `set_text()` method with the output of the input method interaction, resulting in either a green or red box to appear over the letter, symbolising either the correct or incorrect letter being inputted.

## Input Method
The input methods are built as subclasses of the `IInputMethod` protocol within modules that can be imported into the testing page. THe `IInputMethod` super class requires the subclasses to support a `on_text_update(callback)` function to react to the user typing in real time.

