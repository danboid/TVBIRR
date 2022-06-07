# TVBIRR

TV Box Internet Radio Recorder is an ansible playbook to configure a X96 Max+ (or similar, if you adjust the playbook) TV box to act as an internet radio streaming and recording device that you can operate entirely with the infrared remote included with the TV box. Its source includes a playlist of over 32000 free to play internet radio stations. 

TVBIRR uses music on console (moc) to stream the playlist, keybinder to convert the infrared remote keypresses into commands, JACK for audio playback and streams are recorded to MP3 files using jack_capture. You can see when jack_capture is recording the currently played station because CARD is displayed on the TV box LED display. You can easily customise it to play playlists of locally stored music too.
