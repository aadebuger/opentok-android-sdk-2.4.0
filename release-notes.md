OpenTok Android SDK release notes
=================================

New features and changes
------------------------

Version 2.4.0

* This version adds support for testing on the Genymotion x86 emulator and the official Android x86 emulator images in    
  combination with the Intel HAXM software for VM Acceleration.

* This version adds support for screen sharing. When publishing a screen-sharing stream,
  call the `setPublisherVideoType(type)` method of the PublisherKit object and pass in
  `PublisherKitVideoType.PublisherKitVideoTypeScreen`. This optimizes the video encoding
  for screen sharing. It is recommended to use a low frame rate (5 frames per second or lower)
  with screen sharing. 	When using the
  screen video type in a session that uses the OpenTok Media Server, you should
  call the `setAudioFallbackEnabled(boolean enabled)` method of the Publisher object and
  pass in <code>true</code> to disable the audio-only fallback feature, so that the
  video does not drop out in subscribers. See [the OpenTok Media Router and media
  modes](http://tokbox.com/opentok/tutorials/create-session/#media-mode).

  When a stream is created in a session, you can determine the video type of the stream
  (screen or camera) by calling the `getStreamVideoType()` method of the Stream object.
  The type of stream is defined by constants in the StreamVideoType enum: `StreamVideoTypeScreen`
  and `StreamVideoTypeCamera`.

  To publish a screen-sharing stream, you need to implement a custom video capturer for the
  OTPublisherKit object. For sample code that publishes a screen-sharing stream, see the
  "screensharing" sample (in the samples directory).

* You can disable the audio-only fallback feature for a published stream by calling the
  `setAudioFallbackEnabled(enabled)` method of the PublisherKit object (passing in `false`).
  The audio-fallback feature is available in sessions that use the [OpenTok Media
  Router](http://tokbox.com/opentok/tutorials/create-session/#media-mode). With the audio-fallback
  feature enabled (the default), when the OpenTok Media Router determines that a stream's quality
  has degraded significantly for a specific subscriber, it disables the video in that subscriber
  in order to preserve audio quality. The audio-fallback feature is enabled by default.

* This version of the SDK takes advantage of hardware acceleration for video decoding on devices
  that support it. These devices include the Nexus 5 (with OS before 5.0 Lollipop), the Asus K010,
  the Asus K011, and the Asus K013.

* Updated to use BoringSSL, Google's fork of OpenSSL.

* Fixed an issue with video not recovering when network conditions improve (after video was
  disabled, due to network conditions, in a session using the OpenTok Media Server).

Version 2.3.0

* The reason parameter has been added to the
  `SubscriberKit.VideoListener.onVideoDisabled(SubscriberKit subscriber, String reason)`
  and `SubscriberKit.onVideoDisabled(SubscriberKit subscriber, String reason)`
  methods. This parameter describes the reason why the subscriber video is being
  disabled. In the previous version, this method was only called when the video
  was disabled due to changes in the stream quality (in a session that uses the
  OpenTok Media Router). In version 2.3.0, the method is also called if the
  publisher stops sending a video stream or the subscriber stops subscribing to
  it (and the `reason` parameter value will be set accordingly).

* New methods indicate when a subscriber's video stream starts (when there
  previously was no video) or resumes (after video was disabled):
  `SubscriberKit.VideoListener.onVideoEnabled(SubscriberKit subscriber, String reason)`
  and `SubscriberKit.onVideoEnabled(SubscriberKit subscriber, String reason)`.

* Use the new PublisherKit.AudioLevelListener and SubscriberKit.AudioLevelListener
  classes to monitor changes in the audio levels of publishers and subscribers.
  To use these, call the `setAudioLevelListener(listener)` method of a PublisherKit
  or SubscriberKit object.

* The new `SubscriberKit.VideoListener.onVideoDisabledWarning(subscriber)` method
  is called when the OpenTok Media Router determines that the stream quality has
  degraded and the video will be disabled if the quality degrades more. The new
  `SubscriberKit.VideoListener.onVideoDisabledWarningLifted(subscriber)` method
  is called when the stream quality improves. This feature is only available in
  sessions that use the OpenTok Media Router (sessions with the
  [media mode](http://tokbox.com/opentok/tutorials/create-session/#media-mode)
  set to routed), not in sessions with the media mode set to relayed. The
  SubscriberKit class also has protected `onVideoDisabledWarning()` and
  `onVideoDisabledWarningLifted()` methods that you can implement (instead of
  the VideoListener methods) when you subclass SubscriberKit.

* This version includes a new custom audio driver API. This lets you use
  custom audio streams and define the audio device used to capture and render
  audio data. The following new classes support the custom audio driver API:

  * AudioDeviceManager -- Use this class to set the app to specify a custom
  audio device for use in the app.

  * BaseAudioDevice -- Defines an audio device for use in a session.

  * BaseAudioDevice.AudioBus -- The audio bus marshals audio data between the network and
  the audio device.

  * BaseAudioDevice.AudioSettings -- Defines the format of the audio.

* You can optimize the speaker usage for voice-only calls by calling
  `AudioDeviceManager.getAudioDevice().setOutputMode(BaseAudioDevice.VOICE_COMMUNICATION)`.
  This sets the app to use the headset speaker (and the loudspeaker is disabled), which is
  preferable in voice-only apps.

* This build improves the video quality under poor network conditions.

* The Session.PublisherListener and the `Session.onPublisherAdded(publisher)`
  and `Session.onPublisherRemoved(publisher)` methods have been removed. You
  can monitor the `PublisherKit.PublisherListener.onStreamCreated(stream)` and
  `PublisherKit.PublisherListener.onStreamDestroyed(stream)` methods to determine
  when a publisher's stream is created and destroyed.


Version 2.2.1

* Updated to use version 1.0.1h of OpenSSL.
* The `Session.unpublish()` method now takes a PublisherKit parameter, instead of a Publisher
  parameter. (The Publisher class extends PublisherKit.)

Known issues
------------

* In a session with the [media mode](http://tokbox.com/opentok/tutorials/create-session/#media-mode)
  set to relayed, only one client can subscribe to a stream published by an Android device.

* You cannot subscribe to screen-sharing streams in version 2.3 of the OpenTok Android SDK.
  You must upgrade your app to use version 2.4.0.
