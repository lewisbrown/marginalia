#!/usr/bin/env rake
#-*-ruby-*-
# Add your own tasks in files placed in lib/tasks ending in .rake,
# for example lib/tasks/capistrano.rake, and they will automatically be available to Rake.

require File.expand_path('../config/application', __FILE__)

Ideas::Application.load_tasks

task :prod do
  ENV['GIT_REMOTE_NAME'] = 'marginalia'
  ENV['HEROKU_APP_NAME'] = 'marginalia-io'
end

task :stage do
  ENV['GIT_REMOTE_NAME'] = 'heroku'
  ENV['HEROKU_APP_NAME'] = 'bugsplat-notes'
end

task :deploy do
  sh "git push #{ENV['GIT_REMOTE_NAME']} master"
  sh "heroku run bundle exec rake db:migrate --app #{ENV['HEROKU_APP_NAME']}"
  sh "heroku ps:restart web --app #{ENV['HEROKU_APP_NAME']}"
end

task :console do
  sh "heroku run rails console --app #{ENV['HEROKU_APP_NAME']}"
end

task :clean_guest_users => :environment do
  users = User.where("email like 'guest%@example.com' and created_at < now() - '1 days'::interval")
  users.each do |user|
    user.notes.each do |note|
      note.destroy
    end
    user.user_emails.each do |email|
      email.destroy
    end
    user.destroy
  end
end

task :export, [:user_id] => :environment do |t, args|
  job = ExportJob.new(args[:user_id])
  job.perform
end
