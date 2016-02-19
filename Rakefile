require "rubygems"
require "tmpdir"

require "bundler/setup"
require "jekyll"

GITHUB_REPONAME = ENV['GITHUB_REPONAME'] # <username>/<repo>
GITHUB_TOKEN = ENV['GITHUB_TOKEN']       # Generate an app token on GitHub
GITHUB_BRANCH = ENV['GITHUB_BRANCH'] || 'gh-pages'

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
    fail if GITHUB_REPONAME.to_s.empty?
    fail if GITHUB_TOKEN.to_s.empty?

    Dir.mktmpdir do |tmp|
      cp_r "_site/.", tmp
      Dir.chdir tmp
      `git init`
      `git add .`
      message = "Site updated at #{Time.now.utc}"
      `git commit -m #{message.inspect}`
      pushed = `git push --force --quiet https://#{GITHUB_TOKEN}@github.com/#{GITHUB_REPONAME}.git master:#{GITHUB_BRANCH}`
    end
  end
end
