# Capistrano Drupal

This gem provides a number of tasks which are useful for deploying Drupal projects. 

Credit goes to railsless-deploy for many ideas here.

## Installation

    # gem install drupal-cap
    
## Usage

Open your application's `Capfile` and make it begin like this:

    require 'rubygems'
    require 'railsless-deploy'
    require 'drupal-cap'
    load 'sites/default/deploy'

You should then be able to proceed as you would usually, you may want to familiarise yourself with the truncated list of tasks, you can get a full list with:

    $ cap -T

## Git Ignore

The deployment script expects that sites/default/files and sites/default/settings.php will not be checked into git. Add them to .gitignore in your project.

## Roadmap

- Split out the tasks into indivual files/modules
- Use drush aliases
- Support install profiles
- Support composer

An example sites/default/deploy.rb

    set :application, "mydrupalproject.hu"
    set :repository,  "ssh://git@git.mygitserver.hu:2222/mydrupalproject"
    set :branch,      "master"
    set :scm,         :git
    set :deploy_via,  :remote_cache

    set :deploy_to,   "/var/www/mydrupalproject"

    # X, Y, Z have login permissions for this user (public key)
    set :user, "serveruser"
    set :port, 2222

    # set :scm, :git # You can set :scm explicitly or Capistrano will make an intelligent guess based on known version control directory names
    # Or: `accurev`, `bzr`, `cvs`, `darcs`, `git`, `mercurial`, `perforce`, `subversion` or `none`

    role :web, "mydrupalproject.hu"                          # Your HTTP server, Apache/etc
    role :app, "mydrupalproject.hu"                          # This may be the same as your `Web` server

    set :keep_releases, 5

    set :use_sudo, false
    set :copy_exclude, [".git"]

    # required for cPanel servers (removing writable by group permission)
    after 'deploy:create_symlink', 'cpanel:fixchmod'

    desc <<-DESC
      Fix file permissions for cPanel
    DESC
    namespace :cpanel do
      task :fixchmod, :roles => [:web, :app] do
        run "chmod g-w #{current_path}/index.php"
        run "chmod g-w #{current_path}/cron.php"
        run "chmod g-w #{latest_release}"
      end

    end
