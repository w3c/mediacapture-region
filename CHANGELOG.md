# Changelog
### 2021-11-26
* `produceCropId` renamed as `produceCropTarget`.
* Use of `crop-IDs`, which were [UUIDs](https://wicg.github.io/uuid/), were replaced with `CropTarget`, an opaque interface.

### 2021-10-07
* A cloned `BrowserCaptureMediaStreamTrack` now starts out **uncropped**. This makes things simpler in multiple ways:
  1. Simplifies the spec - all tracks now start out **uncropped**, with no exceptions.
  2. Eliminates potential concerns over whether a `Promise` returned by `cropTo()`, once resolved, would signal that new frames are cropped on both the original track and the clone, or only on the original track.
