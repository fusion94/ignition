require 'rake'

namespace :ignite do
  TARGET_RUBY_VERSION = '1.9.3-p327'

  namespace :db do
    desc 'Install PostgreSQL.'
    task :postgres do
      if install_homebrew
        if install_homebrew_package 'postgresql'
          `initdb /usr/local/var/postgres -E utf8`

          FileUtils.mkdir_p "#{ENV['HOME']}/Library/LaunchAgents"
          FileUtils.cp '/usr/local/Cellar/postgresql/9.2.1/homebrew.mxcl.postgresql.plist', "#{ENV['HOME']}/Library/LaunchAgents/"

          `launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist`
        end
      end
    end
  end

  task :all => ['homebrew', 'git', 'ruby', 'rails', 'db:postgres', 'pow']

  desc 'Install Git and hub.'
  task :git do
    if install_homebrew
      if install_homebrew_package 'git'
        puts "\nSetting up Git."
        puts 'What is your name?'
        `git config --global user.name '#{STDIN.gets.strip}'`

        puts "\nWhat is your email address?"
        `git config --global user.email '#{STDIN.gets.strip}'`
      end

      if install_homebrew_package 'hub'
        `echo 'alias git="hub"' >> ~/.bash_profile`

        puts "\nSetting up hub."
        puts 'What is your GitHub username?'
        `git config --global github.user '#{STDIN.gets.strip}'`
      end
    end
  end

  desc 'Install Homebrew.'
  task :homebrew do
    if homebrew_installed?
      puts '** Homebrew already installed. **'
    else
      puts "\n**** Installing Homebrew ****"
      system 'ruby -e "$(curl -fsSkL raw.github.com/mxcl/homebrew/go)"'

      `echo 'PATH="/usr/local/bin:/usr/local/sbin:$PATH"\nexport PATH' >> ~/.bash_profile`
    end
  end

  desc 'Install the Pow web server.'
  task :pow do
    if File.exists? "#{ENV['HOME']}/Library/Application Support/Pow"
      puts '** Pow already installed. **'
    else
      puts "\n**** Installing Pow ****"
      system 'curl get.pow.cx | sh'
    end
  end

  desc 'Install the latest Rails gem.'
  task :rails do
    if %x{rbenv versions}.include? TARGET_RUBY_VERSION
      if %x{~/.rbenv/shims/gem list}.split("\n").map { |x| x.split.first }.include? 'rails'
        puts '** Rails already installed **'
      else
        puts "\n**** Installing Rails ****"
        `~/.rbenv/shims/gem install rails --no-ri --no-rdoc`
        `rbenv rehash`
      end
    else
      puts "\n** You must install Ruby first. **"
      puts 'Would you like to install it now? (y or n)'
      case STDIN.gets.chomp
      when 'y' then Rake::Task['ignite:ruby'].invoke
      end
    end
  end

  desc "Install rbenv, ruby-build, and latest Ruby."
  task :ruby do
    if install_homebrew
      install_homebrew_package 'rbenv'
      install_homebrew_package 'ruby-build'

      if %x{rbenv versions}.include? TARGET_RUBY_VERSION
        puts "** Ruby #{TARGET_RUBY_VERSION} already installed. **"
      else
        puts "\n**** Installing Ruby #{TARGET_RUBY_VERSION} ****"
        system "rbenv install #{TARGET_RUBY_VERSION}"
        `rbenv rehash`
        `rbenv global #{TARGET_RUBY_VERSION}`

        `echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bash_profile`
        `echo 'PATH="$HOME/.rbenv/shims:$PATH"\nexport PATH' >> ~/.bash_profile`
      end
    end
  end

  def homebrew_installed?; File.exists? '/usr/local/bin/brew'; end

  def install_homebrew
    unless homebrew_installed?
      puts "\n** You must install Homebrew first. **"
      puts 'Would you like to install it now? (y or n)'
      case STDIN.gets.chomp
      when 'y' then Rake::Task['ignite:homebrew'].invoke
      when 'n' then return false
      end
    end
    return true
  end

  def install_homebrew_package(name)
    if %x{brew list}.split("\n").include? name
      puts "** #{name.capitalize} already installed. **"
      return false
    end
    puts "\n**** Installing #{name} ****"
    system "brew install #{name}"
    return true
  end
end

desc 'Run all Ignition tasks in ordered succession.'
task :ignite => 'ignite:all'
