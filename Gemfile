source 'https://rubygems.org'

require 'json'
require 'open-uri'
versions = JSON.parse(open('https://pages.github.com/versions.json').read)

gem 'kramdown', '1.5.0'
gem 'markdown'
gem 'github-pages', versions['github-pages']
