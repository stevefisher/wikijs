---
title: Steam Deck Boot Fix
description: 
published: 1
date: 2022-12-22T20:54:05.589Z
tags: 
editor: markdown
dateCreated: 2022-12-22T20:54:05.589Z
---

Here's how to restore SteamOS boot entry if it breaks after an update

1. Start with Steam Deck off.
2. Hold Volume + button and power.
3. Choose "Boot from file".
4. Select esp/efi/steamos/steamcl.efi.
5. Wait until SteamOS boots (there will be a blank screen for some time, but Steam should start after that).
6. Go to desktop mode.
7. Open Konsole.
8. Basic Installation instructions (assuming from a SteamOS command line in desktop mode). Run these commands one after the other.
```bash
git clone https://github.com/jlobue10/SteamDeck_rEFInd/
cd SteamDeck_rEFInd
chmod +x SteamDeck_rEFInd_install.sh
./SteamDeck_rEFInd_install.sh
```
9. Reboot steam os and the dual boot option should be back.