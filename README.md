# Issue

```bash
pnpm i
pnpm dev
# - then navigate to localhost:5173/click
# - click connect
# after you are connect you will a black screen and a button will popup soon after
# behind the scene the app has send a request to bitski provider to sign a message
# the bitski popup is actually there hidden under the black background
# the click button shown is actualy a fake button placed exactly where the bitskip Authorize button is and upon clicking in the button area
# the Authorize button will   clicked instead
# in the console you sould see the resulting signature
```
