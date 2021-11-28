+++
title = "How to install Flutter, Android Studio, and Visual Studio Code in Ubuntu"
subtitle = ""
banner = "https://sorasystem.sirv.com/photos/pawns.jpg"
description = "How to install Flutter, Android Studio, and Visual Studio Code in Ubuntu"
tags = ['Flutter', 'Android', 'VS Code']
date = 2019-01-01
+++

I'm putting up these steps as reference for future upgrades to Flutter.


### Step 1: Install Flutter

- Download the [Flutter zip](https://flutter.dev/docs/get-started/install/linux) from the official site
- Unzip the files
- Afterwards, the extracted files would be in `/home/your-ubuntu-username/Downloads/flutter`
- In your terminal, enter: `sudo nano ~/.bashrc`
- At the bottom, insert: `export PATH="$PATH:$HOME/Downloads/flutter/bin"`
- Exit nano with `Ctrl + x`

<br>

### Step 2: Install Android Studio

- Download the [Android Studio SDK zip](https://developer.android.com/studio) from the official site
- Unzip the files
- Afterwards, the extracted files would be in `/home/your-ubuntu-username/Downloads/android-studio`
- In your terminal, cd into `/home/your-ubuntu-username/Downloads/android-studio/bin`
- Run `sh studio.sh`

- In Android Studio, click `Tools --> Create desktop entry..`
- Go to ``File --> Settings --> Plugins``
- Select ``Marketplace``
- Choose and install ``Flutter``

<br>

### Step 3: Install Visual Studio Code

- Download the .deb file from the official site
- Double-click the .deb file
