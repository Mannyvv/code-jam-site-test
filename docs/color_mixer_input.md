# Color Mixer Input Method


## __Intro__

The `Color Picker` page offers the user a frustrating typing interface, possibly triggering bouts of synesthesia. Users
are tasked with typing a randomly generated phrase, but first they have to find the letters...

## __User Demo__

Below is a brief demo of how a user may interact with the page. 

# ![type:video](./assets/images/color_input/Color_Picker_Demo.mp4)


The first few user inputs are described below:

1. User clicks the spectrum in a region corresponding to the color `lime`, which outputs the letter `l`
2. User toggles the CAPS LOCK on. Note that this does not capitalize the L.
3. User confirms the letter L
4. Note that `l` is highlighted in red in the target text since it is the incorrect character
5. The user clicks black to input backspace and remove the character `l`
6. The user selects `lime` again which now inputs `L`.
7. The user confirms the input
8. The user changes the color hues using the spectrum meter
9. The user removes the `CAPS LOCK`, selects `orange`, then confirms the letter `o`


## Main UI

Users will see the page below:

![image](./assets/images/color_input/Color_Picker_Full_Page.png)


### Typing with the Color Mixer 

# ![image](./assets/images/color_input/Color_Picker_Mixer_Default.png)


The color mixer is the main keyboard. Users can access all 26 letters, backspace, and space by clicking a color. Special
characters and capital letters are accessible via the buttons to the right of the color mixer. The mixer panel displays 
color values in hexadecimal or (Red,Green,Blue). See the image below.

# ![image](./assets/images/color_input/Color_Picker_Full_Color_Hex.png)
# ![image](./assets/images/color_input/Color_Picker_Full_Color_RGB.png)

> Colors correspond to intuitive letters where possible, but some letters are hard to find...

----
### More on Typing 
----
#### Letters

In order to type a **letter** the user

1. Clicks a color on the color palette
2. Clicks the **CONFIRM LETTER** button

> **Letters will not appear in the target output prior to confirmation!!!**

#### Special Characters

- To type the special characters `. ! , ?` press the corresponding button. 
- The `CAPS LOCK` switch controls letter case, which the user can toggle. It defaults to the off position (white) and 
turns on (blue) when clicked.


#### More Details

The first letter of the selected color determines the selected letter. **Black** and **white** correspond to **backspace** 
and **space** inputs, respectively. 

*Letters* must be confirmed using the **CONFIRM LETTER** button. Special characters, *backspace*,and *space* inputs will 
automatically display in the target output.

### Help Text

Upon clicking a color, the user sees *the name of the color selected and the corresponding letter* (see image below). 
This information is displayed on the right side of the color panel, above the special character buttons. This text is
meant to aid the user. Users must confirm letters before they are officially typed. Special characters are automatically 
confirmed and displayed, including space and backspace inputs. 

In the image below the user has clicked on the color *fuchsia* which corresponds to the letter f.


![image](./assets/images/color_input/Color_Picker_User_Help_Text.png)

> Clicking `HEX` or `RGB` in the header above changes whether the color value displays as a hex code or as 
> Red-Green-Blue color intensities. This does not affect the color name display text.

----
### More on the Color Mixer
----

The color mixer contains three modes. *Spectrum*, *Tune*, and *Palette*. The image above displays Spectrum mode, which 
is the default mode.

#### Spectrum

Users selects colors by clicking on the map. Users can adjust the spectrum meter to access various hues. Black and white 
are accessible on all levels. 

#### Tune

Users select colors by adjusting the intensities of Red, Green, and Blue. The values can be adjusted by dragging the bar
for a particular color, or by typing an integer (0 to 255 inclusive). The Tuner icon is circled in orange
below. Click the icon to switch to this view mode.

# ![image](./assets/images/color_input/Color_Picker_Mixer_Tuner.png)


#### Palette

Users select colors by clicking within the color palette. The Palette icon is circled in orange in the image below. 
Click it to switch to this view mode.

# ![image](./assets/images/color_input/Color_Picker_Mixer_Palette.png)


> Note: Spectrum mode is recommended.




### Targets, statistics, and cues

The target sentence is displayed in green text. The top of the page also displays a timer and 
typing statistics. The timer begins when the first input is confirmed. Users receive visual clues indicating typing 
accuracy. Correct inputs are highlighted in green while incorrect inputs are highlighted in red. See the two images 
below.

# ![image](./assets/images/color_input/Typing_Stats.png)
# ![image](./assets/images/color_input/Typing_Accuracy_Signals.png)  


----
## __Implementation__
----
The color input page was created by implementing the class `ColorInputComponent`. 

The `niceGUI` framework was beneficial in implementing the page and class, especially in constructing the page layout.
The color mixer, CAPS LOCK, and special character elements were all created using functionality native to `niceGUI`. 
In particular, the color-mixer came from the `Quasar color-picker` component. Color-picker properties were tweaked to 
optimize the page, for example ensuring that the color picker did not minimize. However, many important features were 
readily available. Further styling of the page elements was achieved using standard `CSS` and `Tailwind` properties.

The class consists of 13 methods used to construct the page, and an additional method, `color_input_page` that was used 
for testing the class separately from the main program.

The class `__init__` method sets various attributes or performs methods needed to build the page, track user inputs, and
map colors to text. This includes but is not limited to

- a callback attribute allowing the class to interface with the WPM Tester class
- the current color selected by the user
- the current character selected by the user
- the character confirmed by the user
- ui elements (e.g. color picker and character buttons)
- a color dictionary mapping colors names to unique letters or inputs

In particular, the callback attribute plays a central role in the construction of the class. Rather than using `ui.page`
decorators to generate individual classes, the project was designed to allow all input methods to work through the WPM
tester. This was achieved using the `on_text_update` method which allowed the WPM tester to communicate with the class
whenever text should be updated. Implementing the `on_text_update` method ensured that the `ColorInputComponent`
satisfied the requirements of the protocol defined in `input_method_proto.py`. As previously discussed implementing the
specific page methods as protocols allowed for greater cohesion across the project.

The code handles user click events and establishes the backend logic necessary to map colors to text. For each clickable
page element the class contains uses custom `handler` and `update` methods to update the backend and page state after a 
user click. The `update_helper_text` and `update_confirmation_text` methods serve as helpers to the 
`special_character_handler`, `confirm_letter_handler`, `color_handler`, and `shift_handler` methods. 

The methods `create_ui_content` and `setup_ui_buttons` set up the page layout and button positions, and create the 
specific UI elements necessary for typing (e.g. color mixer, special character buttons).


The `find_closest_member` method is an important backend functions. Each of the 26 letters, backspace, and space map 
to a unique color. This information was stored in the aforementioned dictionary attribute, which contained 28 items. 
The RGB/hex code structure permits the user to select up to 16^6, or nearly 16.8 million colors. The 
`find_closet_member` method maps every possible hex code color to one of the 28 colors in the dictionary. Colors were 
mapped to the "closest" color in the dictionary using their RGB coordinates and the Euclidean distance metric. The class
contains two other static methods that serve as helper functions to `find_closest_member`. The method `hex_to_rgb` 
converts hex_codes to a 3-tuple of RGB values, while `color_dist` uses the RGB tuples to compute the distance between 
two colors

The class also uses the `string` library in base python for easy access to ASCII characters.