HMMVoiceCreation for MARY 5.0

We use the training scripts provided by [HTS](http://hts.sp.nitech.ac.jp/) adapted to the MARY 5.0 platform.
The steps for building a HMM voice for the MARY platform can be summarised in:  
I. [Download MARY TTS including Voice import tools](https://github.com/marc1s/marytts#readme)  
II. [Required programs and files](#stepII)  
III. [Preparing data for training a HMM-voice](#stepIII)  
IV. [Training a HMM-voice](#stepIV)   
V. [Adding a new HMM-based voice in MARY platform](#stepV)  
VI. [Training a HMM-voice in other language](#stepVI)  

***

## <a name="stepII" /> II. Required programs and files
We provide an script to facilitate the checking and installation of the necessary external programs, once MARY TTS has been installed, open a command line shell run the commands:  

    cd $MARY_BASE/lib/external/
    ./check_install_external_programs.sh

With the option **-check**, this script will check if the necessary programs and versions are installed (that is, the programs can be found in the PATH or in the paths provided by the user).
With the option **-install** this script will try to download and install the necessary programs in: $MARY_BASE/lib/external/bin (if problems, it will suggest how to install manually the programs).

If you have already installed some of the required programs, please include their paths in the PATH variable or provide the paths, for example:

    $MARY_BASE/lib/external/check_install_external_programs.sh -check /path/to/htk/bin /path/to/Festival/festvox/src/ehmm/bin

This script generates a $MARY_BASE/lib/external/externalBinaries.config file that will be used by the Voice import tools to locate the necessary external programs.

The necessary programs that this script checks are:

**Common tools**
* awk (normally available in linux)
* perl (normally available in linux)
* bc (normally available in linux) 
* tcl supporting snack (available from the Ubuntu/Debian repositories under [tcl](http://packages.ubuntu.com/trusty/tcl))
* [snack](http://www.speech.kth.se/snack/download.html) library for tcl (available from the Ubuntu/Debian repositories under [libsnack2-dev](http://packages.ubuntu.com/trusty/libsnack2-dev) or [tcl-snack-dev](http://packages.ubuntu.com/trusty/tcl-snack-dev)) 
* [sox](http://sox.sourceforge.net/), v13.0 or greater (normally available from common Ubuntu/Debian repositories)

**Tools that require manual installation** 
* HTK-3.4.1 and HDecode patched with HTS-2.2_for_HTK-3.4.1.patch: 
    * [HTK-3.4.1](http://htk.eng.cam.ac.uk/ftp/software/HTK-3.4.1.tar.gz) (you will need to register first)  
    * [HDecode](http://htk.eng.cam.ac.uk/prot-docs/hdecode.shtml) (you will need to register first)  
    * [HTS-2.2_for_HTK-3.4.1](http://hts.sp.nitech.ac.jp/archives/2.2/HTS-2.2_for_HTK-3.4.1.tar.bz2)
* [SPTK-3.4.1](http://downloads.sourceforge.net/sp-tk/SPTK-3.4.1.tar.gz)  
* [hts_engine_API-1.05](http://downloads.sourceforge.net/hts-engine/hts_engine_API-1.05.tar.gz)  
* EHMM, available in ``$MARYBASE/lib/external/ehmm.tar.gz`, or with Festvox version 2.1 or newer: ​http://festvox.org/download.html

**Installation instructions**

All tools that require manual installation must be extracted, compiled and installed using the standard ``./configure``, ``make``, ``sudo make install`` workflow. 
Before you start building the tools, make sure the following programs and libraries are installed on your system, since they are needed during the compilation process:
* gcc, g++
* libc6 (libc6-i368 if you're on a 64-bit system)

In order to compile and install the patched HTK tools, first extract the [HTK-3.4.1](http://htk.eng.cam.ac.uk/ftp/software/HTK-3.4.1.tar.gz) archive as follows:

    $ tar -zxvf /path/to/HTK-3.4.1.tar.gz
    
Then, staying in the same working directory, extract the [HDecode](http://htk.eng.cam.ac.uk/prot-docs/hdecode.shtml) archive "on top", then switch to the ``htk`` directory, which now contains the HTK and HDecode sources:

    $ tar -zxvf /path/to/HDecode-3.4.1.tar.gz
    $ cd htk
    
Now, extract and apply the .patch file from the [HTS-2.2_for_HTK-3.4.1](http://hts.sp.nitech.ac.jp/archives/2.2/HTS-2.2_for_HTK-3.4.1.tar.bz2) archive:

    $ tar -zxvf /path/to/HTS-2.2_for_HTK-3.4.1.tar.bz2 HTS-2.2_for_HTK-3.4.1.patch
    $ patch -p0 --strip=1 < HTS-2.2_for_HTK-3.4.1.patch
    
The HTK tools are now ready to be compiled and installed. It is important that all binaries produced during the build process are copied to a known location that can then either be passed as an argument to the aforementioned ``check_install_external_programs.sh`` script or simply added to the system's ``$PATH``. The easiest way to ensure this is to pass a path to the ``configure`` script using the ``--prefix`` option and making sure the ``bin`` directory under that path is contained in the system's ``$PATH`` variable. This can be achived as follows:

    $ export PATH=$PATH:/path/to/external/programs/bin
    $ ./configure --prefix=/path/to/external/programs
    
Once the configuration completes successfully, you can run ``make`` to compile the HTK tools. If you encounter errors during the compilation process, try re-running the ``configure`` script with the option ``--disable-hslab``, then run ``make`` again.

Run ``make install`` to copy the binaries to the specified path.

The procedure is very similar for [SPTK-3.4.1](http://downloads.sourceforge.net/sp-tk/SPTK-3.4.1.tar.gz) and [hts_engine_API-1.05](http://downloads.sourceforge.net/hts-engine/hts_engine_API-1.05.tar.gz): extract the archives, ``configure`` using the same path for the ``--prefix`` option, ``make`` and finally ``make install``. 

Keep in mind that, depending on the write permissions of the destination directory, you may need root privileges to successfully run ``make install``.

Finally, install EHMM by extracting the sources, running ``make`` and adding the resulting binaries to your path:

    $ make
    $ export PATH=$PATH:/path/to/ehmm/bin

Once all tools have been installed successfully, (re-)run ``check_install_external_programs.sh check`` and verify that the output looks something like this (the paths will be different on your system, of course):

    MARY_BASE=/home/julian/tts/marytts

    awk: /usr/bin/awk
    ok

    perl: /usr/bin/perl 
    ok

    tclsh: /usr/bin/tclsh 
    tclsh was found and supports snack
    ok

    bc: /usr/bin/bc
    bc: checking -l (mathlib)
    ok

    sox: /usr/bin/sox
    ok

    HTK HHEd exists
    HTK version: 3.4.1
    HTK HHED contains HTS commands like CM 
    HTK ok

    hts_engine exists
    hts_engine: /home/julian/usr/bin/hts_engine
    ok

    SPTK mgcep exists
    SPTK mgcep: /home/julian/usr/bin/mgcep
    SPTK gmm exist, SPTK version >= 3.2
    ok

    festvox ehmm exists
    festvox ehmm: /home/julian/tts/marytts/lib/external/ehmm/bin/ehmm
    ehmm exist
    ok

    ________________________________________________________________
    Programs status (detailed information above):
    The following paths should be in the PATH variable
      awk: /usr/bin
      perl: /usr/bin
      bc: /usr/bin
    The following paths are used when running HMMVoiceConfigure
      tclsh: /usr/bin
      sox: /usr/bin
      hts/htk: /usr/local/bin
      hts_engine: /home/julian/usr/bin
      sptk: /home/julian/usr/bin
    This path is used when running the EHMMlabeler
      ehmm: /home/julian/tts/marytts/lib/external/ehmm/bin

    List of paths in: /home/julian/tts/marytts/lib/external/externalBinaries.config

**Mac-specific notes:**

In addition to the above comments, the following notes may be helpful to get the dependencies for HMM-based voice building set up on a Mac.

* Snack: Installing snack on a mac seems to require a few steps. Here is one way:

  - Install ActiveTcl from http://www.activestate.com/activetcl/downloads;
  - get snack source code from http://www.speech.kth.se/snack/download.html, followed the mac/README to install it (but the files end up in a new /lib/ folder in the root of the hard drive);
  - move the snack2.2 dir from /lib/ to /Library/Tcl:

            $ sudo mv /lib/snack2.2 /Library/Tcl/


* wget: The dependency installer expects wget to be available, so we get it e.g. with MacPorts:

        $ sudo port install wget

***

##<a name="stepIII" /> III. Preparing data for training a HMM-voice

In your voice building directory execute the step-by-step procedure in [VoiceImportTools](VoiceImportToolsTutorial.md) to make sure that the data, sound (wav) and text files are in the correct place and format. As a result of this step your voice building directory should contain a wav and text directories. 
In your voice building directory run the voice import tools:

    $MARY_BASE/target/marytts-builder-<VERSION>/bin/voiceimport.sh

And run the following components:

1- Run the **AllophonesExtractor** to create the prompt_allophones directory required in the next step. This component requires the MARY server.

2- Run the **EHMMlabeler** to label automatically the wav files using the corresponding transcriptions. If the pauses at the beginning and end of your recordings are longer than 0.2 seconds, you might consider to reduce these pauses using the tool: Convert recorded audio (as explained in NewLanguageSupport No. 9) to trim initial and final silences.

The EHMMLabeler procedure might take several hours. For running EHMMLabeler, please use the settings editor of this component to set, according to your festvox installation, the variable:

    EHMMLabeler.ehmm  = ../festvox/src/ehmm/bin/

The result of this step is a ehmm/lab directory.

3- Run the **LabelPauseDeleter**. Please use the settings editor of this component to set the variable:

    LabelPauseDeleter.threshold  =  10

The result of this step is a lab directory.

4- Run the **PhoneUnitLabelComputer**, this procedure has as input the lab directory and will create as an output the phonelab directory.

5- Run the **TranscriptionAligner**, this program will create the allophones directory.

6- Run the **FeatureSelection**, this program will create a mary/features.txt file, it requires the MARY server running. Select here all the features and save the file.

7- Run the **PhoneUnitFeatureComputer**, to extract context feature vectors from the text data. This procedure will create a phonefeatures directory. For running this component the MARY server should be running as well.

8- Run the **PhonelabelFeatureAligner**, this procedure will verify alignment between "phonefeatures" and "phonelabels".

As a result of previous steps you should have in your voice building directory:

    phonefeatures directory
    phonelab directory
    mary/features.txt file
    $MARY_BASE/lib/external/externalBinaries.config 

***
##<a name="stepIV" /> IV. Training a HMM-voice

9- Run the **HMMVoiceDataPreparation** to set up the environment to create a HMM voice and check if the required external programs, text and wav files are available and in the correct paths.

10- Run the **HMMVoiceConfigure**, the default setting values are already fixed for the arctic slt voice, some path settings depend on your installation, and will be taken from $MARY_BASE/lib/external/externalBinaries.config

If running configure for other voice, for example a male German voice, please use the settings editor of this component to set the variables:

    HMMVoiceConfigure.dataSet      =  german_set_name
    HMMVoiceConfigure.speaker      =  speaker_name 
    HMMVoiceConfigure.lowerF0      =  40  (male=40,  female=80)  
    HMMVoiceConfigure.upperF0      =  280 (male=280, female=350)

Using the settings editor of this component you can also change other variables like using LSP instead of MGC, sampling frequency, etc., the same as you would do when running "make configure + parameters" with the original HTS scripts.

11- Run the **HMMVoiceFeatureSelection**, this program reads the mary/features.txt file (created in step 6), and generates the file mary/hmmFeatures.txt. The hmmFeatures.txt file contains extra features, apart from phone and phonological features, that will be used to train HMMs. You can select or delete on the window extra context features (all can be used).

12- Run the **HMMVoiceMakeData**, to run the HTS procedure "make data". The HMMVoiceMakeData procedure is similar to the original HTS scripts with additional sections for calculating voicing strengths (str), for mixed excitation, and composing training data files from mgc, lf0 and str files. This component executes in the hts/data/ directory:

    make mgc lf0 str-mary cmp-mary list scp 

The label directory and the mlf files in MARY are done in java with the Voice Import Tools: HMMVoiceMakeData.makeLabels()
The questions file in MARY is done in java with the Voice Import Tools: HMMVoiceMakeData.makeQuestions()

Particular procedures can be repeated isolated fixing the particular settings for this component. For example, if the procedure that creates strengths (in the str directory) has to be repeated with a different set of filters (various sets of filter are provided in hts/data/filters/), set:

    HMMVoiceMakeData.makeSTR       =  1
    HMMVocieMakeData.makeCMPMARY   =  1

all the other variables in 0, and run again the component. (In this case you need to run makeCMPMARY again because you need to compose again the vectors mgc+lf0+str).

The procedures can be repeated manually as well, going to the hts/data directory and running "make str-mary" and "make cmp-mary".

13- Run the **HMMVoiceMakeVoice**, here again particular training steps can be repeated selecting them (setting in 1, all the others in 0) from the settings of this component. 

**NOTE:** Since some people have reported some issues ([issue70](https://github.com/marytts/marytts/issues/70), [issue54] (https://github.com/marytts/marytts/pull/54)) using the java wrapper, it is strongly recomended to better run this step on the command line:

    perl scripts/Training.pl scripts/Config.pm > logfile &

as is normally done with the original HTS scripts. 

These procedure is rather complicated and take several hours, so please time to time have a look to logfile, it contains detailed information about latest command used and parameters.

In order to run again particular steps, you just need to activate the corresponding steps in Config.pm, as is normally done with the original HTS scripts.

***

##<a name="stepV" /> V. Adding a new HMM-based voice in MARY

14- Run the **HMMVoiceCompiler** to compile the voice to be used in MARY TTS. The default setting values of this component are already fixed.  
Once the voice is compiled, follow the instructions in [Publishing-a-MARY-TTS-Voice](https://github.com/marytts/marytts/wiki/Publishing-a-MARY-TTS-Voice) to install the voice.

***

##<a name="stepVI" /> VI. Training a HMM-voice in another language

If you are creating a voice in other language you will need to specify:

**Minimal NLP components**: if you are creating a new voice from scratch, for example following the steps in [NewLanguageSupport](https://github.com/marytts/marytts/wiki/New-Language-Support), you will need to create Minimal NLP components for the new language. These minimal components are necessary to run the MARY server in the new language and extract context features (phonefeatures directory). 

**Phoneme set**: contained in $MARY_BASE/marytts-lang-xx/src/main/resources/marytts/language/xx/lexicon/allophones.xx.xml , where xx corresponds to the new language. 

After creating the minimal components, you will need wave files (in a wav directory) and the corresponding transcriptions (one file per wave file in a text directory).

Afterwards follow the instructions as normal from step 1. Provide general settings for:

    db.gender    =  male  (or female)
    db.locale    =  new_language locale (according to your minimal NLP components, ex. tr for Turkish, te for Telugu, etc.)
    db.marybase  =  /path/to/mary/base/
    db.voicename =  new_language_voice_name



