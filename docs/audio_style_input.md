# __Audio Input Method__ 
---
## __Introduction__

This component is a fun and kind of annoying typing test. It works and gets the job one, but it definitely is the wrong tool for the job. Fitting right into the code jam theme of this year.

It highlights the absurdity of using a vinyl record interface to type text. Users interact with the typing test by using the vinyl record controls like **play**, **pause**, **skip forward/backward**, and other buttons to select characters.

When the user presses **play**, a song begins and characters are displayed in sequence as the music plays. Users watch the characters and, when they see the one they want, they press **record** to send it to the typing test component. Characters can be **lowercase letters**, **uppercase letters**, or **punctuation**, and the user can cycle through **three different tracks**, each corresponding to a type of character. 


## __User Interface__
### Components
Starting Page
![image](./assets/images/audio_input/intro.png)
Initial Setup
![image](./assets/images/audio_input/setup.png) <br>
Buttons<br>
![image](./assets/images/audio_input/buttons.png)<br>
Letter Pill<br>
![image](./assets/images/audio_input/letter.png)

### Styling

- `color_style.ColorStyle` — defines a consistent theming system for the app. 
  
---
### Detailed Flow

- User clicks the play button → triggers vinyl to spin, characters to cycle
- Letter spinner updates asynchronously → new letter is displayed in the UI.  
- `select_char()` fires when the record button is pressed → sends the current string of typed characters with the new character to the Typing Test Component via `TextUpdateCallback`.  
- Typing Test Component updates the sentence display → shows which letters were typed correctly or incorrectly and tracks the user's position in the sentence.  
- WPM/WPH is calculated only after the user reaches the end of the sentence with correct typing.  
- UI updates → record animation and letter chip continue spinning, creating the “wrong tool for the job” experience.
---
## __Demo__
Button Usage
![type:gif](./assets/images/audio_input/button_usage.gif)

Complete a Sentence
![type:gif](./assets/images/audio_input/completed.gif)

Sound Effects - __Press Play!__
![type:video](./assets/images/audio_input/with_sound.mp4)
> Unmute for best experience
---

## __Code__ 

##### Core Libraries & Classes

This component uses a combination of a main framework, utility libraries, and interface classes. Below is a breakdown of the most important parts.

#####Main Framework

- <em>`NiceGUI` — provides web-based UI elements, layouts, and events, handling interactivity, buttons, audio controls, and visual updates for the spinning record and character spinner. NiceGUI components also allowed me to play music and sounds when needed.

#####Utility Library

- <em>`Asyncio` — used to create a timer so the vinyl record image can spin, the characters cycle through their sets, and buttons can be monitored for actions such as rewind, forward, pause, and play. Also used to play sounds when buttons are pressed.

#####Interface Classes

- <em>`input_method_proto.IInputMethod` — the interface that this component implements to connect with the typing test module.
- <em>`TextUpdateCallback` — a function passed from the typing test module that allows this component to send selected characters back to the tester.
         
---

##More In Depth

- `cycle_char_select()` — Cycles between uppercase, lowercase, and punctuation character sets.  
- `spin_continuous()` — Runs asynchronously to continuously rotate the vinyl record.  
- `letter_spinner_task()` — Updates the character label asynchronously, cycling through the selected set.  
- `start_spinning()` / `stop_spinning()` — Controls vinyl record rotation.  
- `speed_boost()` — Temporarily increases spin speed for animations like fast-forward or rewind.  
- `toggle_play_pause()` / `play_pause_handler()` — Handles play/pause logic for music and character spinner.  
- `_select_letter_handler()` — Handles letter selection and triggers the text callback.  
- `_delete_letter_handler()` — Deletes the last character in the user text.  
- `_add_space_handler()` — Adds a space to the user text.  
- `forward_3()` / `rewind_3()` — Skip multiple letters with accompanying sound effects and spin animations.  
- `setup_buttons()` — Creates NiceGUI buttons and binds them to event handlers.

```mermaid
classDiagram
  IInputMethod <|.. AudioEditorComponent

  class AudioEditorComponent {
    +__init__()
    +create_intro_card() tuple[ui.card, ui.button]
    +create_main_content() tuple[ui.column, ui.image, ui.chip, ui.row, ui.row]
    +setup_buttons()
    +cycle_char_select()
    +spin_continuous()
    +letter_spinner_task()
    +start_spinning(clockwise: bool)
    +stop_spinning()
    +speed_boost(final_direction: int)
    +toggle_play_pause()
    +play_pause_handler()
    +on_play()
    +on_pause()
    +play_rewind_sound()
    +play_fast_forward_sound()
    +forward_3()
    +rewind_3()
    -_select_letter_handler()
    +start_audio_editor()
    +on_text_update(callback: TextUpdateCallback)
    +select_char(char: str)
    -_delete_letter_handler(char: str)
    -_add_space_handler(char: str)
  }

  class IInputMethod {
    +on_text_update(callback: TextUpdateCallback)
  }

```


Logic Flow
```mermaid
graph LR
  A[Start] --> B{Play/Pause Pressed?};

  B -->|No| A; 
  B -->|Yes - Play| C[Letter Spinner Active];
  B -->|Yes - Pause| F[Spinner Paused];

  C --> D{Desired Letter Visible?};
  D -->|No - Not yet| E[Wait/Adjust Spinner];
  E --> G{Adjust Spinner?};
  G -->|Fast Forward| C;
  G -->|Rewind| C;
  G -->|Change Character Set| C;
  G -->|No Adjustment / Wait| C;

  D -->|Yes - Record| H[Record Button Pressed];
  H --> I[Character Added to Text];
  I --> J{More Characters Needed?};

  F --> B; 

  J -->|Yes| C;
  J -->|No| K[Finish Typing Session];
```
