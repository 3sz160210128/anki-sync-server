ankisyncd
=========

[Anki][] is a powerful open source flashcard application, which helps you
quickly and easily memorize facts over the long term utilizing a spaced
repetition algorithm. Anki's main form is a desktop application (for Windows,
Linux and macOS) which can sync to a web version (AnkiWeb) and mobile
versions for Android and iOS.

This is a personal Anki server, which you can sync against instead of
AnkiWeb. It was originally developed to support the flashcard functionality
on Bibliobird, a web application for language learning.

This version is a fork of [jdoe0/ankisyncd](https://github.com/jdoe0/ankisyncd).
It supports Python 3 and Anki 2.1.

[Anki]: https://apps.ankiweb.net/
[dsnopek's Anki Sync Server]: https://github.com/dsnopek/anki-sync-server

Installing
----------

0. Install Anki. The server currently works well with 2.1.1. Running the server
   with other versions might work, but things might break, so do it at your own
   risk. Anki 2.0.x definitely isn't going to work, as it's written in Python 2,
   whereas `ankisyncd` runs only on Python 3.5 and later. If for some reason
   you can't get the supported Anki version easily on your system, you can use
   `anki-bundled` from this repo:

        $ git submodule update --init
        $ cd anki-bundled
        $ pip install -r requirements.txt

   Keep in mind `pyaudio`, a dependency of Anki, requires Python 3 and PortAudio
   headers to be present before running `pip`. If you can't or don't want to
   install these, you can try [patching Anki](#running-ankisyncd-without-pyaudio).

1. Install the dependencies:

        $ pip install webob

2. Modify ankisyncd.conf according to your needs

3. Create user:

        $ ./ankisyncctl.py adduser <username>

4. Run ankisyncd:

        $ python -m ankisyncd

Setting up Anki
---------------

### Anki 2.1

Create a new directory in `~/Anki/addons21` (name it something like ankisyncd),
create a file named `__init__.py` containing the code below and put it in
`~/Anki/addons21/ankisyncd`.

    import anki.sync

    addr = "http://127.0.0.1:27701/" # put your server address here
    anki.sync.SYNC_BASE = addr + "%s"

### Anki 2.0

Create a file (name it something like ankisyncd.py) containing the code below
and put it in `~/Anki/addons`.

    import anki.sync

    addr = "http://127.0.0.1:27701/" # put your server address here
    anki.sync.SYNC_BASE = addr
    anki.sync.SYNC_MEDIA_BASE = addr + "msync/"

Running `ankisyncd` without `pyaudio`
-------------------------------------

`ankisyncd` doesn't use the audio recording feature of Anki, so if you don't
want to install PortAudio, you can edit `anki-bundled/anki/sound.py` and
`anki-bundled/requirements.txt` to exclude `pyaudio`:

`ed` version:

    $ echo '/# Packaged commands/,$d;w' | tr ';' '\n' | ed anki/sound.py
    $ echo '/^pyaudio/d;w' | tr ';' '\n' | ed requirements.txt

`sed -i` version:

    $ sed -i '/# Packaged commands/,$d' anki/sound.py
    $ sed -i '/^pyaudio/d' requirements.txt

Manual version: remove every line past "# Packaged commands" in anki/sound.py,
remove every line starting with "pyaudio" in requirements.txt
