# WASD Input

This input method is a parody of the inconvenience of text entry in RPG Maker games. Naviage around an on-screen keyboard using wasd + space or arrow keys + enter.

## Playthrough video

![type:video](./assets/wasd_playthrough.mp4)

## Implementation Details

The keys on the keyboard are stored as a tuple of strings, which must must be rectangular (all strings mut be the same `len`).

```python
KEYBOARD_KEYS: tuple[str, ...] = (
    "ABCDEFGabcdefg",
    "HIJKLMNhijklmn",
    "OPQRSTUopqrstu",
    "VWXYZ. vwxyz!\N{SYMBOL FOR BACKSPACE}",
)
```

`\N{SYMBOL FOR BACKSPACE}` is hard-coded to act as backspace instead of entering the literal character.

Functionallity is implemented using a keyboard event hook through `ui.keyboard(on_key=self.handle_key)`
