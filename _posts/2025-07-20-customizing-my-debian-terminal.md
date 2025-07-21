# Customizing the Debian Terminal

## Introduction

I would like to note before beginning that this is on Debian 12 with KDE Plasma as my desktop environment. The colors may not be the same if you are on a different desktop environment or if you use a different terminal emulator.

Ever since I switched to Debian, I've been using the default shell configuration. Recently, after I riced my Kali VM up a bit, I was inspired to start doing some terminal ricing on my host Debian machine. In this process, I went from the default shell configuration that looks like this:

![Default Debian Terminal](/assets/images/default-debian-terminal.png)

to one that looks like this:

![Customized Debian Terminal](/assets/images/customized-debian-terminal.png)

## Zsh

For my custom terminal setup, I decided to use zsh over Debian's default bash. Honestly, you could probably get away with using regular bash, but I liked the fact that zsh has better customization options within it, and zsh was what I was using on Kali (which meant I could copy paste the same zsh prompt configuration).

### Zsh Installation

To begin, let's install zsh. Fortunately, this is very simple:
`sudo apt install zsh`

### Oh My Zsh Installation

After installing Zsh, I'm going to go ahead and install [Oh My Zsh](https://ohmyz.sh/#install), which is a framework that makes editing the zsh configuration much easier (and includes some templates as well, if you don't like the prompt I use).
To do this, we can run:
`sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`
During the process, you will get a prompt asking if you want to set zsh to your default shell; I recommend selecting yes. Once this is done, you will notice that your prompt will now look like this: `→  ~`

## Konsole

Konsole will be my terminal emulator of choice. There is no particular reason behind this other than it is what ships with Debian and I'm used to it. Feel free to experiment with different terminal emulators! On Konsole, I had a couple options I wanted to change (and some that are needed to use zsh instead of bash)

### Making a New Profile

This step is required to run zsh instead of bash. Before we do this, we need to double check where our zsh is installed. To do this, we can use `which zsh`. Make sure to remember the directory it gives you. To start creating our new profile, we open Konsole, then on the top bar we click Settings then Manage Profiles.

![Manage Profiles Button Selected](/assets/images/manage-profiles-button-selected.png)

Once we are on this page, we will click New on the right to start making a new profile.
There are only a few options we need to change, those being the profile name, and the command.
We can set the profile name to whatever we wish, I chose to use "Main".
For the command, we want to use the directory that `which zsh` gave us. For me, this was `/usr/bin/zsh`. Make sure to check the 'Default profile' box so Konsole will launch into this profile by default. Once we are done in the General tab, it should look something like this:

![Finished Konsole Profile](/assets/images/new-debian-profile-finished.png)

Additionally, if we click the Scrolling tab on the left, we can set our Scrollback to unlimited. I find this incredibly useful as sometimes commands will output very large amounts of text that's long enough to move previous commands beyond the scrollback distance.

![Scrollback Settings](/assets/images/scrollback-unlimited-setting.png)

I also opted to uncheck "Highlight the lines coming into view setting" in this area. This will remove the blue lines on the left of the terminal when new text appears.

![Blue lines](/assets/images/blue-bar.png)

### Changing Misc. Konsole Settings
Next, there are a few settings you can change to make Konsole look/act nicer.
In the top bar of Konsole, click Settings then "Configure Konsole"

![Configure Konsole Button Selected](/assets/images/configure-konsole-button-selected.png)

We have a couple different tabs on the left with settings we will want to change:

- General
  - Remember window size: unchecked
    
![Konsole General Tab Config](/assets/images/konsole-general-config.png)

- Tab Bar / Splitters
  - Show: Always
    - This will make the new tab button visible even when there is only 1 tab
  - Position: Above terminal area
  - Show 'New Tab' button: checked
    
  ![Konsole Tab Bar / Splitters Config](/assets/images/konsole-tab-bar-config.png)

- Temporary Files
  - Scrollback file location: User cache directory (~/.cache/konsole)
 
  ![Konsole Temporary Files Config](/assets/images/konsole-temp-files-config.png)

## .zshrc Prompt
Now that we have that stuff out of the way, we can get to working on our prompt! We will want to open `~/.zshrc` with our favorite editor (personally, I will be using visual studio code). Once we open up our .zshrc file, we will want to add the following to the end:

```
{% raw %}
PROMPT=$'%F{%(#.blue.green)}┌──${debian_chroot:+($debian_chroot)─}${VIRTUAL_ENV:+($(basename $VIRTUAL_ENV))─}(%F{%(#.red.blue)}%* %b%F{%(#.blue.green)}| %F{%(#.red.blue)}%n@%m%b%F{%(#.blue.green)})-[%F{reset}%(6~.%-1~/…/%4~.%5~)%b%F{%(#.blue.green)}]\n└─%(#.%F{red}#.%F{blue}$)%b%F{reset} '
{% endraw %}
```
This line will make our prompt look like the following:

![Finished Prompt Image](/assets/images/prompt-finished.png)

I adapted this from Kali Linux's default prompt. If you are looking to customize this further, [this website](https://zsh.sourceforge.io/Doc/Release/Prompt-Expansion.html) describes the custom variables usable inside the PROMPT variable.

## Setting up eza
[eza](https://github.com/eza-community/eza) is essentially a modernized version of ls that comes with more customization features.
### Installing
To install eza, we can run the following commands:

```
sudo apt update
sudo apt install -y gpg
sudo mkdir -p /etc/apt/keyrings
wget -qO- https://raw.githubusercontent.com/eza-community/eza/main/deb.asc | sudo gpg --dearmor -o /etc/apt/keyrings/gierens.gpg
echo "deb [signed-by=/etc/apt/keyrings/gierens.gpg] http://deb.gierens.de stable main" | sudo tee /etc/apt/sources.list.d/gierens.list
sudo chmod 644 /etc/apt/keyrings/gierens.gpg /etc/apt/sources.list.d/gierens.list
sudo apt update
sudo apt install -y eza
```

(These commands are ripped from their website)

### Aliasing ls to eza

Next up, we can edit our .zshrc to tell it to replace the ls command with eza command. Open up .zshrc again and add the following lines:

```
alias ls='eza --icons --color=always --group-directories-first'
alias ll='eza -alF --icons --color=always --group-directories-first'
alias la='eza -a --icons --color=always --group-directories-first'
alias l='eza -F --icons --color=always --group-directories-first'
alias l.='eza -a | egrep "^\."'
alias tree='eza --icons --color=always --group-directories-first --tree'
```

This will make `ls` actually run `eza --icons --color=always --group-directories-first`. This also adds some shortcuts (`ll`, `la`, `l`, `l.`) and remaps `tree` to use eza's tree function. Make sure to restart your terminal for these to work! Every time the .zshrc is changed, you must restart your terminal (or open a new tab) for the changes to take effect.

### Installing Nerd Font Icons

If we were to try it now, we would see this:

![Image of ls output no icons](/assets/images/ls-output-no-icon.png)

What's up with all the icons!? They're not working! This is because we have not yet installed a font to allow these icons to appear correctly.
We can download the font required [here](https://github.com/ryanoasis/nerd-fonts/releases/download/v3.4.0/NerdFontsSymbolsOnly.zip). Once this zip is downloaded, open it and install both .ttf files.

Note: If you are getting an error about not being able to read the font, you likely need to switch from a Wayland session to an x11 session. Do this by logging out, clicking the session type in the bottom left, and switching it to x11. After this, log back in and try again.

Now let's try it again.

![Image of ls output with icons](/assets/images/ls-output-with-icon.png)

It works!

### Changing eza color output

Now I want to change some of the formatting and colors for the file names outputted by eza. Primarily, I would like to remove the bolding on file names (I hate the bolding style). To do this, we can add the following lines to our `.zshrc` file.
```
LS_COLORS='rs=0:di=34:ln=36:mh=00:pi=40;33:so=35:do=35:bd=40;33;00:cd=40;33;00:or=40;31;00:mi=00:su=37;41:sg=30;43:ca=00:tw=30;42:ow=34;42:st=37;44:ex=32:*.tar=31:*.tgz=31:*.arc=31:*.arj=31:*.taz=31:*.lha=31:*.lz4=31:*.lzh=31:*.lzma=31:*.tlz=31:*.txz=31:*.tzo=31:*.t7z=31:*.zip=31:*.z=31:*.dz=31:*.gz=31:*.lrz=31:*.lz=31:*.lzo=31:*.xz=31:*.zst=31:*.tzst=31:*.bz2=31:*.bz=31:*.tbz=31:*.tbz2=31:*.tz=31:*.deb=31:*.rpm=31:*.jar=31:*.war=31:*.ear=31:*.sar=31:*.rar=31:*.alz=31:*.ace=31:*.zoo=31:*.cpio=31:*.7z=31:*.rz=31:*.cab=31:*.wim=31:*.swm=31:*.dwm=31:*.esd=31:*.avif=35:*.jpg=35:*.jpeg=35:*.mjpg=35:*.mjpeg=35:*.gif=35:*.bmp=35:*.pbm=35:*.pgm=35:*.ppm=35:*.tga=35:*.xbm=35:*.xpm=35:*.tif=35:*.tiff=35:*.png=35:*.svg=35:*.svgz=35:*.mng=35:*.pcx=35:*.mov=35:*.mpg=35:*.mpeg=35:*.m2v=35:*.mkv=35:*.webm=35:*.webp=35:*.ogm=35:*.mp4=35:*.m4v=35:*.mp4v=35:*.vob=35:*.qt=35:*.nuv=35:*.wmv=35:*.asf=35:*.rm=35:*.rmvb=35:*.flc=35:*.avi=35:*.fli=35:*.flv=35:*.gl=35:*.dl=35:*.xcf=35:*.xwd=35:*.yuv=35:*.cgm=35:*.emf=35:*.ogv=35:*.ogx=35:*.aac=36:*.au=36:*.flac=36:*.m4a=36:*.mid=36:*.midi=36:*.mka=36:*.mp3=36:*.mpc=36:*.ogg=36:*.ra=36:*.wav=36:*.oga=36:*.opus=36:*.spx=36:*.xspf=36:*~=90:*#=90:*.bak=90:*.old=90:*.orig=90:*.part=90:*.rej=90:*.swp=90:*.tmp=90:*.dpkg-dist=90:*.dpkg-old=90:*.ucf-dist=90:*.ucf-new=90:*.ucf-old=90:*.rpmnew=90:*.rpmorig=90:*.rpmsave=90:*.py=93'
export LS_COLORS
```
If you want to change these colors around, I recommend looking [here](https://gist.github.com/thomd/7667642). This post does not mention it, but you can also change color per file extension. For example: `*.py=93` changes .py files to an unbolded yellow color. This color scheming was grabbed from Kali linux and then edited.

After these changes, our `ls` should look like so:

![Image of unbolded ls](/assets/images/ls-output-unbolded.png)

Looks great! Except for some of the symbols...

### Setting up eza config

If you look closely, you will notice that some symbols aren't exactly what you would expect. For example, let's look at the symbols for .zip and other compressed file formats.

![Image of wrong icon for compressed files](/assets/images/ls-zip-icon-before.png)

I believe this issue is caused due to the [seen icon](https://unicodes.jessetane.com/%EF%90%90) having the same Unicode code as the default zip icon used by eza. I also had issues with .jar files showing an eight-ball icon, but I did not have the same issue when recreating these steps in a virtual machine.

First, we have to make the eza config: 
```
sudo mkdir /etc/eza
sudo touch /etc/eza/theme.yml
```
Then, we have to tell zshrc where this config is. Add the following line to your .zshrc:
`export EZA_CONFIG_DIR=/etc/eza`

Now we can get to editing the icons! Open /etc/eza/theme.yml with your preferred editor and paste the following in and/or change it to your liking:
```
extensions:
    jar: {icon: {glyph: }} #Uses 8ball icon for some reason
    log: {icon: {glyph: 󱂅}} #Normally uses 

    #Compressed file extensions
    zip: {icon: {glyph: 󰿺}}
    rar: {icon: {glyph: 󰿺}}
    7z: {icon: {glyph: 󰿺}}
    gz: {icon: {glyph: 󰿺}}
    bz2: {icon: {glyph: 󰿺}}
    tar.gz: {icon: {glyph: 󰿺}}
    tgz: {icon: {glyph: 󰿺}}
    xz: {icon: {glyph: 󰿺}}
```
(You will be able to paste the icons properly even if your browser shows empty squares for the icons)
If you would like to customize these icons, I recognize reading [this](https://github.com/eza-community/eza/blob/main/man/eza_colors-explanation.5.md) and also looking at this [example theme file](https://github.com/eza-community/eza/blob/main/docs/theme.yml).

## Results
That wraps it up! We should now have a terminal that looks like the following:

![Completed Terminal](/assets/images/finished-terminal-output.png)
