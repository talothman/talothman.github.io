source 'https://rubygems.org'

# Specify Ruby version - GitHub Pages uses 3.3.4
# See: https://pages.github.com/versions/
ruby '>= 3.0' if ENV['CI'] || ENV['GITHUB_ACTIONS']

# GitHub Pages gem bundles Jekyll and all compatible plugins
# This ensures local builds match GitHub Pages behavior
gem 'github-pages', '~> 232', group: :jekyll_plugins

# Plugins (included in github-pages but explicit for clarity)
group :jekyll_plugins do
  gem 'jekyll-sitemap', '~> 1.4'
  gem 'jekyll-seo-tag', '~> 2.8'
end

# Windows and JRuby timezone support
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem 'tzinfo', '>= 1', '< 3'
  gem 'tzinfo-data'
end

# Performance booster for watching directories on Windows
gem 'wdm', '~> 0.1', platforms: [:mingw, :x64_mingw, :mswin]