# JPEG Video Compression using Block Matching

Motion-compensated video coding in Python: block matching with mean squared error, motion vectors and residual blocks, built with OpenCV.

## Overview

This project was divided into two parts: encoding and decoding. I used MSE (mean squared error) in block matching. Then I calculated the motion vector. The next step was to subtract each block from its best-matched block. In the decoding part, I had residual frames and motion vectors for each block in each frame, assuming I will also use frames 0, 15, 30 and 45 as reference frames, like in the previous project but using blocks. Finally, all Y, Cb and Cr channels are merged, converted back to RGB, and the video is created again from the sequence of frames.

The work was done as the practical project of a *Video and Audio Technology* course. A related, simpler approach is in [JPEG Video Compression using Frame Differencing](https://github.com/ARKhaled149/JPEG-Video-Compression-using-Frame-Differencing).

## Pipeline

### Encoding

1. **Frame extraction**: read the input video (`demo44.mp4`) with OpenCV and save every frame (640 × 360) to `OriginalFrames/`.
2. **Colour space**: convert each frame to YCbCr and save the three channels separately to `YFrames/`, `CBFrames/` and `CRFrames/`.
3. **Blocks**: slice every Y frame into an 8 × 8 grid of 64 blocks with `image_slicer` (`YBlocks/`).
4. **Block matching**: for each block of frame *i*, compute the MSE against all 64 blocks of frame *i + 1* and keep the block with the minimum error.
5. **Motion vectors**: the offset between a block's position and the position of its best match.
6. **Residuals**: subtract the current block from its best-matched block and save the result (`YFinalBlocks/`).

### Decoding

7. **Reassembly**: put the 64 residual blocks of each frame back together into full Y frames (`YFinalFrames/`).
8. **Merging**: merge the Y, Cb and Cr channels (`AllFrames/`) and convert back to RGB (`FinalFramesF/`).
9. **Video**: write the frame sequence to `ProjectVideo.avi` (DIVX codec, 15 frames per second).

## Results

Frame 5 at each stage of the pipeline:

| Original | Y channel | Y after block matching (residual) | Merged output frame |
|---|---|---|---|
| ![Original frame](OriginalFrames/frame5.jpg) | ![Y channel](YFrames/y5.jpg) | ![Residual Y frame](YFinalFrames/YFinalFrame5.jpg) | ![Output frame](FinalFramesF/FFrame5.jpg) |

The output video is [`ProjectVideo.avi`](ProjectVideo.avi). In the committed code the decoding step merges the residual Y frames with the original chroma channels, so the output frames show the residual detail rather than a fully restored picture.

## Tech stack

| Area | Used |
|---|---|
| Language | Python 3 (script and Jupyter notebook) |
| Video and images | OpenCV (`cv2`), Pillow, `image_slicer` |
| Maths | NumPy, scikit-learn (`mean_squared_error`) |
| Plotting | Matplotlib |

## Repository structure

```
.
├── Project.py           # full pipeline, one function per step
├── Project.ipynb        # the same code as a Jupyter notebook
├── main.py              # the same code without notebook cell markers
├── Project CC.txt       # text copy of main.py
├── ProjectVideo.avi     # output video
├── OriginalFrames/      # 69 extracted frames
├── YFrames/             # 69 Y (luma) frames
├── CBFrames/, CRFrames/ # 69 chroma frames each
├── YBlocks/             # 4,416 Y blocks (64 per frame)
├── YFinalBlocks/        # 4,352 residual blocks (64 per frame, 68 frames)
├── YFinalFrames/        # 68 reassembled residual Y frames
├── AllFrames/           # 68 merged YCbCr frames
└── FinalFramesF/        # 68 output frames in RGB
```

## How to run

1. Install the dependencies:

   ```
   pip install opencv-python numpy pillow image_slicer scikit-learn matplotlib
   ```

2. The input video `demo44.mp4` is not part of this repository; provide your own video.
3. All file paths in `Project.py` are hard-coded absolute Windows paths on a `D:` drive. Change them to your own folders first.
4. Each step is a function (`YCBCR`, `YBlocks`, `Encoding`, `CreateYFF`, `Merging`, `YCBCRtoRGB`, `CreateVideo`) whose call is commented out below it. Uncomment and run the calls one at a time, in this order.

## Author

Abdelrahman Abdelrahman — https://abdelrahman.eu
