require "rubygems"
require "tmpdir"
require "open3"

require "bundler/setup"
require "jekyll"

GITHUB_REPO = ENV['GITHUB_REPO'] # <username>/<repo>
GITHUB_TOKEN = ENV['GITHUB_TOKEN']   # Generate an app token on GitHub
GITHUB_BRANCH = ENV['GITHUB_BRANCH'] || 'gh-pages'

GIT_USER_NAME = ENV['GIT_USER_NAME'] || `git config --get user.name`
GIT_USER_EMAIL = ENV['GIT_USER_EMAIL'] || `git config --get user.email`

namespace :site do
  desc "Generate blog files"
  task :generate do
    Jekyll::Site.new(Jekyll.configuration({
      "source"      => "src",
      "destination" => "_site"
    })).process
  end

  desc "Generate and publish blog to gh-pages"
  task :publish => [:generate] do
    fail 'No GitHub repo specified' if GITHUB_REPO.to_s.empty?
    fail 'No GitHub token set' if GITHUB_TOKEN.to_s.empty?
    fail 'No Git user name to use' if GITHUB_TOKEN.to_s.empty?
    fail 'No Git user email to use' if GITHUB_TOKEN.to_s.empty?

    puts "Pushing to #{GITHUB_REPO} #{GITHUB_BRANCH}"

    Dir.mktmpdir do |tmp|
      cp_r "_site/.", tmp
      Dir.chdir tmp

      fail unless system "git init"
      fail unless system %Q[git config user.name "#{GIT_USER_NAME}"]
      fail unless system %Q[git config user.email "#{GIT_USER_EMAIL}"]
      fail unless system "git add ."
      message = "Site updated at #{Time.now.utc}"
      fail unless system "git commit -m #{message.inspect}"

      o, e, s = Open3.capture3 "git push --force --quiet https://#{GITHUB_TOKEN}@github.com/#{GITHUB_REPO}.git master:#{GITHUB_BRANCH}"
      puts o.gsub(/#{GITHUB_TOKEN}/, '[secure]')
      puts e.gsub(/#{GITHUB_TOKEN}/, '[secure]')

      fail 'Git push failed' if s.exitstatus > 0
    end
  end
end
