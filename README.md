# Ignition

Set up your Mac OS X computer for Ruby/Rails development.

## Usage

You must first have the Command Line Tools for Xcode installed. You can obtain them through Xcode itself ([App Store link](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)) or as a standalone installer ([download link](https://developer.apple.com/downloads/download.action?path=Developer_Tools/command_line_tools_os_x_mountain_lion_for_xcode__october_2012/xcode451cltools_10_86938200a.dmg)). Both options are free, but accounts are required.

Run `./ignite` or `rake ignite` from within the Ignition directory to install everything at once.

Installing everything takes a while, but there are a few prompts while it's running so pay attention for them.

You can also use `rake` to install individual components:

    rake ignite:db:postgres  # Install PostgreSQL.
    rake ignite:git          # Install Git and hub.
    rake ignite:homebrew     # Install Homebrew.
    rake ignite:pow          # Install the Pow web server.
    rake ignite:rails        # Install the latest Rails gem.
    rake ignite:ruby         # Install rbenv, ruby-build, and latest Ruby.

I suggest reloading your shell between individual components, and at the end of a full install.

## Compatibility

I have only tested this on freshly formatted Mac OS X 10.8 (Mountain Lion), with Xcode 4.5.1, and/or the Command Line Tools release from Oct. 3, 2012. I make no claims that it will work on other systems with other versions of the above software.

## Limitations

It does very little configuration other than adding the bare minimum to `.bash_profile` and `.gitconfig` to get things running. Feel free to configure everything to your liking afterwards.

Currently, Ignition has no built-in way to uninstall or update components, but the components themselves usually have that functionality built into them.

----

Copyright &copy; 2012 Joe Bilt.
