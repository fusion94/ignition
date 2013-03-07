require 'rake'

namespace :ignite do
  TARGET_RUBY_VERSIONS = ['1.8.7-p371', '1.9.2-p320', '1.9.3-p392', '2.0.0-p0']

  namespace :db do
    task :all => ['postgres', 'redis']

    desc 'Install PostgreSQL.'
    task :postgres do
      if install_homebrew
        if install_homebrew_package 'postgresql'
          `initdb /usr/local/var/postgres -E utf8`

          FileUtils.mkdir_p "#{ENV['HOME']}/Library/LaunchAgents"
          FileUtils.ln_s "/usr/local/opt/postgresql/homebrew.mxcl.postgresql.plist", "#{ENV['HOME']}/Library/LaunchAgents/"

          `launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist`
        end
      end
    end

    desc 'Install Redis.'
    task :redis do
      if install_homebrew
        if install_homebrew_package 'redis'
          FileUtils.mkdir_p "#{ENV['HOME']}/Library/LaunchAgents"
          FileUtils.ln_s "/usr/local/opt/redis/homebrew.mxcl.redis.plist", "#{ENV['HOME']}/Library/LaunchAgents/"

          `launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.redis.plist`
        end
      end
    end
  end

  task :all => ['homebrew', 'git', 'ruby', 'rails', 'db', 'pow']

  task :db => ['db:all']

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
      end
    else
      puts "\n** You must install Ruby first. **"
      puts 'Would you like to install it now? (y or n)'
      case STDIN.gets.chomp
      when 'y' then Rake::Task['ignite:ruby'].invoke
      end
    end
  end

  desc "Install rbenv, ruby-build, rbenv-bundler, and latest Ruby."
  task :ruby do
    if install_homebrew
      install_homebrew_package 'rbenv'
      install_homebrew_package 'ruby-build'
      install_homebrew_package 'rbenv-bundler'

      TARGET_RUBY_VERSIONS.each do |version|
        if %x{rbenv versions}.include? version
          puts "** Ruby #{version} already installed. **"
        else
          puts "\n**** Installing Ruby #{version} ****"
          `export CFLAGS=-Wno-error=shorten-64-to-32`
          system "rbenv install #{version}"
          `rbenv global #{version}`
          `rbenv rehash`
          ENV.delete('RBENV_VERSION')
          `~/.rbenv/shims/gem install rbenv-autohash --no-ri --no-rdoc`
          `~/.rbenv/shims/gem install bundler --no-ri --no-rdoc`
          `rbenv rehash`
        end
      end

      `echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bash_profile`
      `echo 'PATH="$HOME/.rbenv/shims:$PATH"\nexport PATH' >> ~/.bash_profile`
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
