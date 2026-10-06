# Bus Occupancy & Wheelchair Detection

An embedded machine-learning project for the **Arduino Nano 33 BLE Sense** that uses a camera and an on-device object-detection model to count passengers on a bus and flag when a wheelchair is present.

## How it works

1. An **OV7675 camera** captures a frame, which is resized and cropped to the model's 96×96 input.
2. An **Edge Impulse** object-detection model runs on the board and detects `person` and `wheelchair` objects (confidence threshold 0.6).
3. **Occupancy logic** tracks a person from first detection to when they leave the frame:
   - first seen on the left half of the frame (x < 48) → **entry**, occupancy +1
   - first seen on the right half → **exit**, occupancy −1 (never below 0)
4. A wheelchair detection toggles an **accessible seat** status (`WHEELCHAIR PRESENT` / `Clear`).
5. Occupancy and wheelchair status are printed over serial at 115200 baud.

Sample output:

```
>>> ENTRY  Occupancy: 3
------------------------
  Occupancy:  3
  Wheelchair: YES
------------------------
```

## Contents

| File | Description |
| --- | --- |
| `MooreLiamB00875265_Code_And_Link.zip` | Contains the Arduino sketch (`nano_ble33_sense_camera_modified_use.ino`) and a PDF linking to the Edge Impulse project |

Edge Impulse project: https://studio.edgeimpulse.com/public/972208/latest

## Hardware

- Arduino Nano 33 BLE Sense
- OV7675 camera module

## Setup

1. Install the Arduino IDE with the **Arduino Mbed OS Nano Boards** core.
2. Install the **Arduino_OV767X** library.
3. Export the model from the Edge Impulse project as an **Arduino library** (`Bus_Occupancy_BLE_COM683_CW2_inferencing`) and install it. The library is not included in this repo.
4. Open the sketch, select the Nano 33 BLE Sense, upload, and open the Serial Monitor at 115200 baud.
5. Send `b` over serial to stop inferencing.

## Evaluation

**Strengths:** runs fully on-device with no cloud connection, and the logic is simple and easy to follow. It combines two functions (occupancy and accessibility) from a single camera model.

**Limitations:**
- Direction is guessed from where a person first appears, so crossing the frame in the wrong direction or standing still gives wrong counts.
- Designed for one person at a time. Several people entering together are counted as one.
- Frames are low resolution (96×96) with a 2 second delay between captures, so fast movement can be missed.
- Counts are only shown over serial, with no persistent storage or wireless reporting yet.

## Credits

The camera and inference code is adapted from the Edge Impulse Arduino example (Apache 2.0, © 2022 EdgeImpulse Inc.), with occupancy and wheelchair logic added.
