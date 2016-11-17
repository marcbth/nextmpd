# nextmpd with Debian Jessie Patches
# (Ultimate DSD fork)

Have been trying to get one MPD that works for all, and this fork is almost perfect now on a Odroid C2/ Rpi2.

Here is the features

Link to this fork on Github as below

https://github.com/xxxbugxxxx/MPD

-SACD ISO support -no need to covert to DSF/DFF, directly play SACD ISO file -DSD_U32 based Native DSD support (Not DOP) for certain XMOS based USB interface.

    Adaptive up sampling, aka upsample 44.1Khz multiples to 352.8khz, and up sample 48k multiples to 384khz (currently hard coded in DecoderAPI.cxx, you have to change the code to taylor to your DAC capbilities) (https://github.com/xxxbugxxxx/MPD/tr...e-auto-changer branch)

This is probably the best MPD with all DSD related feature all in one now.

Next quest would be PCM to DSD upsampling( one SoX fork supports this,however MPD does not, may need somebody to mod MPD)

process to install

before using this fork please take a look at the following article about how to compile mpd, it will help you solve a lot of problems. Please take a look at the volumio mpd compiling guide if you have compiling problems. This fork is compiled on rpi2, odroid c2, both works. If it does not build on your platform, mostly likely missing libs.

http://volumio.readthedocs.io/en/lat...pilingmpd.html

sudo apt-get install build-essential automake autoconf libtool pkg-config libcurl4-openssl-dev intltool libxml2-dev libgtk2.0-dev libnotify-dev libglib2.0-dev libevent-dev dh-make

sudo apt-get install libupnp4-dev libboost1.55-dev libmpdclient-dev libsystemd-dev libicu-dev libexpat1-dev

git clone https://github.com/xxxbugxxxx/MPD cd MPD

./autogen.sh ./configure --enable-sacdiso --disable-iso9660

make install CPPFLAGS="$(pkg-config --cflags glib-2.0)" LIBS="(pkg-config --libs glib-2.0)"

Modify mpd.conf based on your configuration

Need to add the following portion for sacdiso plugin to work

decoder { plugin "sacdiso" no_downmixes "true" no_short_tracks "true" playable_area "multichannel" (not set by default, shows both tracks) playable_area "stereo" }

Here is native DSD setup method. (only works for XMOS based USB interface)

audio_output {

type "alsa"

name "Output"

device "hw:1,0" // your usb interface

dop "no" //set yes for DOP, set no for native dsd

}

Note: For this fork Native DSD to work you have to 1) XMOS USB interface, please refer to lintweaker's site for details

https://github.com/lintweaker/xmos-native-dsd

2) have ALSA 1.0.29 or later, Linux kernel has to be right one to have the native DSD patch lintweaker put together,

3) set the MPD.conf accordingly

For example, for the following setup:

1) Gustard X-20U DAC, it has Xmos interface, 2) Volumio 2 on RPI, Volumio 2 on Odroid C2 (linux kernel already patched) and alsa version is 1.0.28(no go),need to update to 1.0.29 3) set MPD. conf alsa session as below, then it should work, if it does not , check 1-3 first.

audio_output {

type "alsa" name "Output" device "hw:1,0" // your usb interface dop "no" //set yes for DOP, set no for native dsd

}

Note: there are a few dependencies needed,

expat, libupnp, glib2.0, and glib2.0 path may need to be tweaked. If there is build error, most likely it is missing lib.

After fixing a missing lib, don't forget to do "./autogen.sh" and "./configure --enable-sacdiso --disable-iso9660"

otherwise it won't help.

Also, for DSD-native to work, alsa-lib has to be 1.0.29 or above. linux kernel has to be patched for your USB DAC.

details refer to https://github.com/lintweaker/xmos-native-dsd.

use dpkg -l to find libasound2 's version and remove and build from source as needed.

And this work is based on couple of genius's work.

It is based on Max Kellerman's MPD 0.19.15,

and Maxim V.Anisiutkin 's SACD ISO fork http://git.musicpd.org/cgit/manisiutkin/mpd.git

and Hicaoc's patch to MPD 0.19.15 for DSD_U32 native mode. https://github.com/hicaoc

http://www.musicpd.org
original Music Player Daemon Readme below
Music Player Daemon

A daemon for playing music of various formats. Music is played through the server's audio device. The daemon stores info about all available music, and this info can be easily searched and retrieved. Player control, info retrieval, and playlist management can all be managed remotely.

For basic installation information see the INSTALL file.
Users

    Manual
    Forum
    IRC
    Bug tracker

Developers

    Protocol specification
    Developer manual

Legal

MPD is released under the GNU General Public License version 2, which is distributed in the COPYING file.
