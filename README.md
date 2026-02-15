# dots

I wanted a place to tie together my Nix corfig repos and give an overview. I've
become quite proud of this setup. Just think of this page as blog post.

First, I'd like to explain what Nix is and why it's so awesome. Nix is a whole
cloud of things, it's a package manager, a build system, a programming
language, an operating system, a way of life, and probably more that I haven't
learned about yet. The important thing is what it allows me to do, which is
install and configure my system and programs declaratively. To understand what
that means, I should juxtapose declarative with imperative. To do something
imperatively means to explicity state how things are done step-by-step. This is
the standard paradigm of setting up software. To do things declaratively is the
opposite, it means to declare what is to be done, not how. What this means for
setting up a computer is that with Nix, I'm able to simply state a list of
programs and the settings I want them to have, and I don't need to worry about
how that is done, Nix just handles it. The really cool thing about this is that
it allows me to, in a single file (or in many for organization), describe how I
want my computer to be setup, run a command, and boom, let thy will be done!
There are a few huge benefits here. One, the way I've configured my computer is
not fragile and dependent on the continued existence of my physical hardware.
My computer could melt in a fire, and as long as I have my configuration backed
up online, I can have everything back in as long as it takes me to run through
the OS installation wizard, connect to wifi, and run a script. This allows me
to accumulate a lifetime of customizations, building and building until I reach
computing Nirvana. I never have to start over with a fresh machine, or fear
that anything else will reset me. Maybe equally important, Nix configuration
files can by synced across as many computers as you like, and used to exactly
recreate your system. I currently have 5 computers running my Nix setup, so a
change I make to one is instantly applied to all 5. If I install and configure
a program once, it's done, forever. Instantly reproduced across all systems.
All of this makes customizing my computer no longer a waste of time, but a
permanent investment in my developer tools and productivity. More and more all
the time, I am psyched to use my machine, it's always becoming more refined,
more pleasing to use, more capable. 

This really is a programmer's operating system, you're basically writing a
program that builds your system, but now that I've learned to use it, there is
no operating system I've tried or heard of that doesn't seem silly by
comparison. Not only is it great fun to tinker with, it's a serious tool for
DevOps/IT. I hope to someday use this technology in my professional life, aside
from just using my personal setup for work. 

I have 3 primary repos that I use in the setup of my system:

- [nix-modules](https://github.com/gusjengis/nix-modules)
- [.home-manager](https://github.com/gusjengis/.home-manager)
- [nix-install-script](https://github.com/gusjengis/nix-install-script)

The `nix-modules` repo contains my system level configurations that I want to
share between all of my machines. I have it split into modules so that locally
each system can turn on and off certain modules depending on it's purpose. This
repo lives in the /etc/nix-modules/ directory. Just next to it, there is a
local-only /etc/nixos/ folder. I back this up as well, but it is machine
specific. It contains an automatically generated file that installs whatever the
hell is needed to make that specific hardware work (I never need to touch or
look at it), and a configuration file that I have basically empty by default.
That file is where I declare which modules are enabled on that system, as well
as anything I want to add that's specific to that hardware (Ex: Keybinds with
keyd). It's what allows that computer to differentiate itself, despite the shared config.

For example, I just set up a headless home assistant server, it inherits my
config, but I turned off a bunch of modules it didn't need to keep it minimal,
like the desktop environment. Any modules it does enable will still sync.

The `.home-manager` repo is similar, modules and all, but it configures my home
directory. This does a lot of the heavy lifting. It contains most of my
programs, all of my raw config files, it contains many important scripts, and
it's where I declare all of the repos that I want on my system. This folder has
a couple gitignored machine specifc files, modules.nix and local.nix. The
former is obvious enough, it's where I set any non-default module booleans I
want on the machine, the latter is just a spot for misc local-only config,
which I have yet to use for much.

The `nix-install-script` repo contains a script that I use to install Nix on
new machines. As I descirbed before, I just run a graphical install wizard,
reboot, connect to wifi, curl and run the script with one command, let it do
it's thing, reboot again, and everything is back. 

### High-level system map

```text
fresh NixOS install
        |
        v
nix-install-script (bootstrap script)
        |
        +--> /etc/nix-modules/      <- nix-modules repo (shared system modules)
        |
        +--> /etc/nixos/            <- local machine config (hardware + module toggles)
        |
        +--> ~/.home-manager/       <- .home-manager repo (user programs + dotfiles + scripts)
```

- `nix-install-script`: creates the base layout on a fresh system and pulls in the repos.
- `nix-modules`: shared NixOS modules used across all machines.
- `/etc/nixos`: machine-specific NixOS config for hardware and local choices.
- `.home-manager`: user-level packages, configs, scripts, and repo sync logic.

### Repo syncing 

My home-manager repo contains a few scripts that I use to sync all of my repos
across machines. I have a few files where I define lists of pairs of locations
on the system and urls for repositories I want cloned. When I run my sync_repos
script with the '''sync''' alias, all repos are cloned or synced with pull, and
any that have uncommitted changes or conflicting local commits are skipped.
Repos that fail to sync spawn notifications that remind me to reconcile with
upstream.

### Interop 

I have all of my machines setup to run tailscale so that they're all on the
same virtual LAN no matter where they are. This is setup automatically with
each new machine. This has enabled me to setup a shared set of ssh keys that
allow any one of them to ssh into any other. Even cooler, I discovered waypipe,
and used it to setup a custom launcher that allows me to see all computers on
the network, select one, see it's application launcher remotely, and launch any
graphical app on that system as if it's a local window. This all adds up to
having complete access to everything from any one of my computers. One of which
is an Asahi Linux M2 Mac, which is Arm, so this has unlocked all x86-only
programs on that machine. This virtual network has also allowed me to easily
access my home assistant server from anywhere, both the web UI and the system
it'self. Something cool about waypipe is that it even works without a desktop
environment on the remote machine. My home assistant server is just running
tty, but I can still use my remote app launcher to run graphical apps like it's
file explorer from anywhere. I still neet to get remote audio working, I
haven't looked into that yet. I'm also thinking about setting up sunshine and
moonlight for remote gaming, but that's not a priority.
