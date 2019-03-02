# Pythonic DJI Ryze Tello Workbench

This repository is work in progress. It aims to contain scripts, documentation, hints and helpful assets to enable one creating an application capable of controlling and monitoring a DJI Ryze Tello drone with Python.

*DJI Ryze released [Tello-Python](https://github.com/dji-sdk/Tello-Python) a few month after this repository came into being, thus this repository is deprecated.*

![DJI Ryze Tello](tello.png)

## Communication

All communication between the DJI Ryze Tello and an application is done by means of IEEE 802.11 frames in a IP-based manner.

### Live streaming

An implementation as exemplary for extraction and reassembly of a h.264 video frame from a snippet of a captured live stream can be found [here](../example/video).

## Credits

- Ryze Technology -- [Tello SDK](https://terra-1-g.djicdn.com/2d4dce68897a46b19fc717f3576b7c6a/Tello%20%E7%BC%96%E7%A8%8B%E7%9B%B8%E5%85%B3/For%20Tello/Tello%20SDK%20Documentation%20EN_1.3_1122.pdf)
- Ryze Technology -- [Tello Python3 Control Demo](https://dl-cdn.ryzerobotics.com/downloads/tello/20180222/Tello3.py)