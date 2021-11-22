# Summary
## Problem Overview
Recall that applications may currently obtain a capture of the tab in which they run using [getDisplayMedia](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getDisplayMedia), either with or without [preferCurrentTab](http://go/get-current-browsing-context-media). Moreover, soon another API will allow similar functionality - [getViewportMedia](https://github.com/w3c/mediacapture-screen-share/issues/155). In either case, the application may then also wish to crop the resulting video track so as to remove some content from it (typically before sharing it remotely). We introduce a performant and robust API for cropping a self-capture video track.

## Core Challenges
Layout can change asynchronously when the user scrolls, zooms or resizes the window. The application [cannot](https://docs.google.com/document/d/1dULARMnMZggfWqa_Ti_GrINRNYXGIli3XK9brzAKEV8/edit#heading=h.vtnc1viphmzf) robustly react to such changes without risking mis-cropping the video track on occasion. The browser therefore needs to step in and help.

## Sample Use Case
Consider a combo-application consisting of two major parts - a video-conferencing application and a productivity-suite application co-existing in a single tab. Assume the video-conferencing uses existing/upcoming APIs such as [getDisplayMedia](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getDisplayMedia) and/or [getViewportMedia](https://github.com/w3c/mediacapture-screen-share/issues/155) and captures the entire tab. Now it needs to crop away everything other than a particular section of the productivity-suite. It needs to crop away its own video-conferencing content, any speaker notes and other private and/or irrelevant content in the productivity-suite, before transmitting the resulting cropped video remotely.

<img width="1344" alt="DocsSidebar" src="https://user-images.githubusercontent.com/22117736/136094995-48beda21-232b-48c6-b4fc-b8d2a9286e69.png">

Moreover, consider that it is likely that the two collaborating applications are cross-origin from each other. They can post messages, but all communication is asynchronous, and it's easier and more performant if information is transmitted sparingly between them. That precludes solutions involving posting of entire frames, as well as solutions which are too slow to react to changes in layout (e.g. scrolling, zooming and window-size changes).

## Goals and Non-Goals
### Goals
* The new API we introduce allows an application which is already in possession of a self-capture video track, to crop that track to the contours of its desired element.
* The API allows this to be done performantly, consistently and robustly.
### Non-Goals
* This API does not introduce new ways to obtain a self-capture video track.
* This API does not introduce mechanisms by which a captured document may control what the capturing document can see.

# Solution
## Solution Overview
A two-pronged solution is presented:
* **Crop-ID production:** A mechanism for tagging an HTMLElement as a potential target for the cropping mechanism.
* **Cropping mechanism**: A mechanism for instructing the user agent to start cropping a video track to the contours of a previously tagged HTMLElement, or to stop such cropping and revert a track to its uncropped state.

## Crop-ID production
We introduce `navigator.mediaDevices.produceCropId()`.
```webidl
partial interface MediaDevices {
  Promise<DOMString>
  produceCropId((HTMLDivElement or HTMLIFrameElement) target);
};
```
Given an HTMLElement, `produceCropId()` produces a UUID that can uniquely identify that element to our second mechanism - the cropping mechanism.
(The `Promise` returned by `produceCropId()` is only resolved when the ID is ready for use, allowing the browser time to set up prerequisites and propagate state cross-process.)

## Cropping mechanism
We introduce a `cropTo()` method, which we expose on all video tracks derived of tab-capture.
```webidl
[Exposed = Window]
interface BrowserCaptureMediaStreamTrack : FocusableMediaStreamTrack {
  Promise<undefined> cropTo(DOMString cropTarget);
};
```
Given a UUID, `cropTo()` starts cropping the video track to the contours of the referenced HTMLElement.
Given an empty string, `cropTo()` reverts a video track to its uncropped state.
"On-the-fly" changing of crop-targets is possible.

# Code Samples
```js
/////////////////////////////////
// Code in the capture-target: //
/////////////////////////////////

const mainContentArea = navigator.getElementById('mainContentArea');
const cropId = await navigator.mediaDevices.produceCropId(mainContentArea);
sendCropId(cropId);

function sendCropId(cropId) {
  // Can send the crop-ID to another document in this browsing context
  // using postMessage() or using any other means.
  // Possibly there is no other document, and this is just consumed locally.
}

/////////////////////////////////////
// Code in the capturing-document: //
/////////////////////////////////////

async function startCroppedCapture(cropId) {
  const stream = await navigator.mediaDevices.getDisplayMedia();
  const [track] = stream.getVideoTracks();
  if (!!track.cropTo) {
    handleError(stream);
    return;
  }
  await track.cropTo(cropId);
  transmitVideoRemotely(track);
}
```
