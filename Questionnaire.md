# Security and Privacy Questionnaire for Screen Capture

## 2.1. What information might this feature expose to Web sites or other parties, and for what purposes is that exposure necessary?
No new information is exposed, except:
If a party within the [browsing context](https://html.spec.whatwg.org/multipage/browsers.html#browsing-context) is engaged in [self-capture](https://w3c.github.io/mediacapture-region/#dfn-self-capture) using **[pre-existing means](https://www.w3.org/TR/screen-capture/)**, and a third party within the same [browsing context](https://html.spec.whatwg.org/multipage/browsers.html#browsing-context) had minted a token and passed it to the first party, then the first party can learn of when a certain region in the document goes in/out of the viewport, by listening to the mute track's mute events.

## 2.2. Do features in your specification expose the minimum amount of information necessary to enable their intended uses?
Yes.

## 2.3. How do the features in your specification deal with personal information, personally-identifiable information (PII), or information derived from them?
Not applicable.

## 2.4. How do the features in your specification deal with sensitive information?
Not applicable.

## 2.5. Do the features in your specification introduce new state for an origin that persists across browsing sessions?
No.

## 2.6. Do the features in your specification expose information about the underlying platform to origins?
No. 

## 2.7. Does this specification allow an origin to send data to the underlying platform?
No.

## 2.8. Do features in this specification enable access to device sensors?
No.

## 2.9. Do features in this specification enable new script execution/loading mechanisms?
No.

## 2.10. Do features in this specification allow an origin to access other devices?
No.

## 2.11. Do features in this specification allow an origin some measure of control over a user agent’s native UI?
No.

## 2.12. What temporary identifiers do the features in this specification create or expose to the web?
This feature allows a website to mint tokens called [CropTarget](https://w3c.github.io/mediacapture-region/#crop-target). These are opaque interfaces which are only meaningful within their [browsing context](https://html.spec.whatwg.org/multipage/browsers.html#browsing-context). They do **not** outlive the browsing session. The party minting the tokens may transfer them to trusted third parties within the [browsing context](https://html.spec.whatwg.org/multipage/browsers.html#browsing-context). The only use these tokens have, is to allow **stripping** information from a video stream by cropping it.

## 2.13. How does this specification distinguish between behavior in first-party and third-party contexts?
Not applicable.

## 2.14. How do the features in this specification work in the context of a browser’s Private Browsing or Incognito mode?
The feature does not behave in any special way in this mode.
Note that this feature is an extension of the [Screen Capture](https://www.w3.org/TR/screen-capture/), that allows stripping away information previously gained through that other feature. If [Screen Capture](https://www.w3.org/TR/screen-capture/) should behave differently in Private Browsing or Incognito modes, is an orthogonal question.

## 2.15. Does this specification have both "Security Considerations" and "Privacy Considerations" sections?
No. This feature is deemed security- and privacy-trivial.

## 2.16. Do features in your specification enable origins to downgrade default security protections?
No.

## 2.17. How does your feature handle non-"fully active" documents?
Not applicable.

## 2.18. What should this questionnaire have asked?
Nothing more is presently required.
