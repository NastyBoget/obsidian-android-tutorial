# Synchronize obsidian data using git on Android

## What you'll get with this tutorial


## Why?



## Tutorial

### 1. Prepare termux apps

- [Termux](https://github.com/termux/termux-app) is an emulator of the Linux terminal (we will use it for running git)
- [Termux:Widget](https://github.com/termux/termux-widget) is useful for making widgets for bash scripts (they will be run in termux)

In documentation of these apps on github, it's recommended to download and install them from https://f-droid.org.

#### 1.1 Install Termux

Download the app [here](https://f-droid.org/packages/com.termux/) and install it on your phone.

#### 1.2 Install Termux:Widget

Download the app [here](https://f-droid.org/en/packages/com.termux.widget/) and install it on your phone.


### 2. Prepare git

The following commands were partly given in [another tutorial](https://gist.github.com/Makeshift/43c7ecb3f1c28a623ea4386552712114).

#### 2.1 Install and configure git

Open the Termux app and run the following commands (one by one):

```bash
pkg install git -y
termux-setup-storage
cd storage/shared
git config --global credential.helper store
git config --global user.email "<your_email>"
git config --global user.name "<The name you want on your commits>"
git config --global pull.rebase true
git config --global --bool branch.master.sync true
```

#### 2.2 Clone and configure the repository

Run `git clone <your repository>` and enter your login when prompted. You may need to create a [personal access token](https://github.com/settings/tokens) if you're using GitHub.

Run `git config --global --add safe.directory /storage/emulated/0/<name of your repository>` to configure your repo, e.g. if we have a repository with the name `obsidian`:

```bash
git clone https://github.com/<username>/obsidian
git config --global --add safe.directory /storage/emulated/0/obsidian
```


### 3. Prepare sync scripts

Copy files `git-sync`, `sync-obsidian` on your phone. In the commands below, we'll use the directory `Documents/Obsidian` for storing these files.

#### 3.1 Prepare git-sync

The source of the `git-sync` script: https://github.com/simonthum/git-sync.

Copy `git-sync` into termux `bin` directory and change permissions to allow its execution:

```bash
cp ~/storage/shared/Documents/Obsidian/git-sync /data/data/com.termux/files/usr/bin/git-sync
chmod 700 /data/data/com.termux/files/usr/bin/git-sync
```

To check the work of the script, you can run the following commands:

```bash
cd ~/storage/shared/<name of your repository>
bash /data/data/com.termux/files/usr/bin/git-sync
```

#### 3.2 Prepare sync-obsidian

If the name of your repo is different from `obsidian`, change the **second** line of the script `sync-obsidian`:

```bash
cd ~/storage/shared/<name of your repository>
```


### 4. Make sync widgets

The following instructions can be found in the documentation of the [Termux:Widget](https://github.com/termux/termux-widget).

#### 4.1 Prepare file system

Make the needed directory and copy `sync-obsidian` there:

```bash
mkdir -p /data/data/com.termux/files/home/.shortcuts
chmod 700 -R /data/data/com.termux/files/home/.shortcuts
cp ~/storage/shared/Documents/Obsidian/sync-obsidian /data/data/com.termux/files/home/.shortcuts/sync-obsidian
```

#### 4.2 Make a widget

Go to the main screen on your phone → hold the screen → choose widgets → choose termux:widget → add (select a specific script from the list, `sync-obsidian` in our case).

For using the widget, you may need to allow the termux app display over other app (settings → apps → advanced → display over other apps → termux).


### 5. Prepare Obsidian

Install and open [Obsidian](https://play.google.com/store/apps/details?id=md.obsidian&hl=en_GB&gl=US).

Click "Open folder as vault" and find the directory with your repository. Then click "use this folder".

Every time when you need to synchronize your data in Obsidian, you can click on your `sync-obsidian` widget (it will close automatically if data were synchronized successfully).
Sometimes the script may finish with errors (no data will suffer). In this case, you need to know how to use [git](https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository) (pull, rebase, commit and push manually).

The following instructions may be useful (open termux and type the commands:

```bash
cd ~/storage/shared/<name of your repository>
git fetch
git rebase origin/master  # you may need to resolve conflicts
git add <your changed files>
git commit -m "phone backup"
git push origin master
```
