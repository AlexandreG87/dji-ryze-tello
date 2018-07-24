#### How is the video streaming done?

* All data for live streaming is send within the payload of 802.11 QoS data frames (type 2, subtype 8).
The transport of the data is done by means of UDP to the destination port 7797.

* The drone splits video data into kind of logical chunks.
A chunk might be send in one piece, thus by just one 802.11 frame.
Due to the limit of size for payload in 802.11 frames, a chunk might be fragmented in the 802.11 layer and send piece by piece, thus by several 802.11 frames.

* The first piece of some chunk contains two bytes in addition to the video data.
This bytes represent two integers which indicate the sequence of parts and pieces and a flag highlighting the last piece.
In an application this integers can be used for an ordered reassembly of the video stream, which is a more robust way than reassembly using the sequence/fragment numbers provided by the 802.11 layer.

* Since 802.11 provides error detection by cyclic redundancy check (CRC), 4 bytes in the payload are used to constitute the frame check sequence (FCS). The FCS is not part of the video data.

#### Where to find the video data?

* The format of the video stream is yuv420p at a resolution of 960x720 pixels.
For compression prior transmission h.264 is used to encode the video stream.

* In order to get one h.264 video frame three ingredients - or NALs in terms of h.264 - are needed: 1. sequence parameter set (SPS), 2. picture parameter set (PPS), 3. keyframe. A keyframe is a slice layer without partitioning instantaneous decoding refresh (non-IDR). Hence the NALs expose unique start prefix codes, they can easily be found in the payload.

* The start prefix codes are as follows: 1. SPS = 0x00000167, 2. PPS = 0x00000168, 3. keyframe = 0x00000165. However, the 6 in each start prefix code could become another digit.

#### How to reassemble a video frame?

* The fragmentation done in the 802.11 layer prior transmission for any keyframe has to be reversed. At least three bytes from the header of the 802.11 logical link control (LLC), namely service access points (SAP), are needed to glue the fragments together.

* All bytes of consecutive NALs have to be put together in valid order to yield a h.264 frame, thus 1 SPS + 1 PPS + 1 keyframe, in which a keyframe consists of n pieces concatenated with the SAP of the 802.11 LLC:

    * 00 00 01 67 .. [approx. 26 bytes]
    * 00 00 01 68 .. [approx. 16 bytes]
    * 00 00 01 65 .. [approx. ((n * (964 + 1040)) + ((n-1) * 912 + x) + ((n-1) * 3)) bytes]

#### How to decode a video frame?

* Piping the video data to an external process is assumed to be the most common use case for decoding.
FFmpeg (and Libav) is well suited to decode h.264 frames through pipes.

* The following command shows an example using FFmpeg for decoding.
Reassembled video data feed to stdin will be decoded, converted to RGB and issued on stdout.

    * ffmpeg -i - -f image2pipe -pix_fmt rgb24 -vcodec rawvideo -

* Im comparison to yuv420p, the size of a RGB frame is doubled to (960 * 720 * 3) bytes.
Nevertheless, further processing of RGB data is widely supported and certainly more simple than dealing with other formats.
