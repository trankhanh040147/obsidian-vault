https://getstream.io/glossary/sdp-munging/

# Session Description Protocol (SDP) Munging

In setting up WebRTC communication, developers may want to customize and fine-tune certain aspects of the data exchange. In particular, developers can directly modify the SDP for negotiation by adding or removing codecs for audio and video. Also, some developers may want to extend the WebRTC communication APIs to provide non-standard audio and video streams. The concept of modifying SDP is called munging.

## What is SDP Munging?

SDP Munging is a content modification mechanism in WebRTC. It involves manually changing the Session Description Protocol content WebRTC receives into a different form before transporting them through the network. You can use SDP Munging to, for example, manually modify audio or video codecs WebRTC automatically configures by default.

## How does SDP Munging Works?

A typical SDP manipulation can include the following:

- Implementation of custom-made media types for WebRTC communication.
- Modifying the frame rate and using variable bitrates to improve the quality of audio and video communication.
- Developers can remove audio and video codecs for specific reasons, such as media quality improvement.

## Benefits of SDP Munging in VoIP Services

- **Optimized call quality**: Modifying media codecs can help optimize audio and video quality for participants under poor network conditions and limited bandwidth.
- **Security improvement**: SDP Munging can help developers prevent security risks by removing any audio and video codecs that may not be secure. **Note**: SDP Munging also has some security concerns.
- **Compatibility with old systems**: Customizing audio and video codecs helps developers provide support for different old device capabilities and non-standard VoIP systems.

## Frequently Asked Questions

#### Is it recommended to use SDP Munging for VoIP applications?

SDP Munging should be implemented by addressing the potential security concerns it can pose. It is recommended to use the default WebRTC API implementations for network negotiations.

#### Does SDP Munging have security vulnerabilities?

Modifying SDP improperly can result in security and incompatibility issues.

#### Is SDP Munging a WebRTC standard?

SDP is a standard in WebRTC. However, munging implementations can differ in WebRTC.

#### Why do developers munge SDP?

People munge SDP for several reasons. In particular, you can modify SDP to limit bandwidth or use specific audio and video codecs. All these should be done carefully to prevent security and incompatibility risks.