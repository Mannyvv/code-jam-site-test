# The platformer input method

## How to use it

Basically, it's a platformer game. You jump around on the platforms. To
type a letter, jump and bump your head on one of the letter tiles -- it
will input the letter. Note that every time you jump, it inverts the
capitalization of the next letter.

![type:video](assets/platformer-tutorial.mp4)

## How it works

The code is split into three parts. The main component (`__init__.py`),
which is embedded into the actual page, the physics simulation
(`platformer_simulation.py`), which takes the user's input and returns
the new positions with gravity and collision, and the renderer
(`platformer_scene_cmp.py`), which is a component that renders the scene
and player. There's also the constants file (`platformer_constants.py`)
which is most of the constants throughout the files.

### Main component

Adds the renderer component and passes messages between the simulation
and the renderer, as well as managing some handlers and the
capitalization state.

### Physics simulation

Takes the current keys pressed from the main component, and moves the
player accordingly. It also performs collision checks and applies
gravity.

### Renderer

The renderer first renders the whole map, then puts it in a parent
element with a fixed size to clip out the stuff outside of the field of
view. It always renders the player in the same spot on the canvas, so
it can just use the CSS `transform` property to move the map inverted
to where the player is.

> Fun fact: the original renderer rerendered every 1/3 tile with an
> individual HTML element. This was super slow, and crashed the app at
> speeds of around 30 FPS or higher. The new method is both a lot
> faster and a lot simpler!

### Constants file

The only interesting thing here is the world definition. The initial
scene is defined as a triple-quoted multiline string where `#` is a
block, spaces are air, and anything else are letters. There's also a
function which transforms the scene into a two-dimensional array with
each character as a cell.
