# Changelog
### 2021-10-07
* A cloned `BrowserCaptureMediaStreamTrack` now starts out **uncropped**. This makes things simpler in multiple ways:
  1. Simplifies the spec - all tracks now start out **uncropped**, with no exceptions.
  2. Eliminates potential concerns over whether an unresolve `Promise` returned by `cropTo()`, once resolved, would apply to both the original track and the clone.