MARY TTS 5.0 is the first release from the thoroughly restructured code base.

It is better supported by automated tests than any previous version of MARY TTS, but it may well be that in practical use some hiccups will appear that have not surfaced in testing. Therefore cautious users are advised to treat this as a beta release.

## New features

### Simpler installation

Installing MARY TTS is now performed by simply unpacking the `zip` or `tar.gz` archive at the target location. No clicking through installer pages anymore. In particular, it is now trivial to install MARY TTS on a server without a GUI connection.

The component installer, bin/marytts-component-installer.sh, still uses a gui; see issue #43 for a workaround.

### Classpath-only user of MARY TTS in your own projects

It is now possible to use MARY TTS, with HMM-based voices at least, simply by placing the right files into the classpath. For example, to use US English voice `cmu-slt-hsmm` in your own code, add the following jar files to your classpath:

    marytts-server-5.0-jar-with-dependencies.jar
    marytts-lang-en-5.0.jar
    voice-cmu-slt-hsmm-5.0.jar

Instead of `marytts-server-5.0-jar-with-dependencies.jar` you can also include the individual dependencies, which can be automated using maven. The source code on github includes examples in the `user-examples` folder.

### New MaryInterface API

Using MARY TTS programmatically gets a lot simpler with MARY TTS 5.0 through the new MaryInterface API. The same API can be used to access the TTS components running within the same java process or as a separate client-server setup. For details, see [[MaryInterface]].

### Emotion Markup Language support

MARY TTS 5.0 includes an implementation of W3C's [Emotion Markup Language](http://www.w3.org/TR/emotionml/) as a means of requesting expressive synthetic speech. The result of course depends on the expressive capabilities of the selected synthesis voice; try out the EMOTIONML example with the German `dfki-pavoque-styles` voice on the [demo page](http://mary.dfki.de:59125/).