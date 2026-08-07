source 'https://rubygems.org'

# Specify Ruby version - GitHub Pages uses 3.3.4
# See: https://pages.github.com/versions/
ruby '>= 3.0' if ENV['CI'] || ENV['GITHUB_ACTIONS']

# GitHub Pages gem bundles Jekyll and all compatible plugins
# This ensures local builds match GitHub Pages behavior
gem 'github-pages', '~> 232', group: :jekyll_plugins

# Plugins (already pulled in by github-pages; listed for clarity).
# Deliberately unversioned: github-pages pins these to exact versions
# (currently jekyll-sitemap 1.4.0, jekyll-seo-tag 2.8.0). Adding our own
# constraint here duplicates that pin and makes the bundle unresolvable the
# day github-pages bumps a plugin past it. Let github-pages be the only pin.
group :jekyll_plugins do
  gem 'jekyll-sitemap'
  gem 'jekyll-seo-tag'
end

# Windows and JRuby timezone support.
# :windows replaces the deprecated :mingw/:x64_mingw/:mswin triple.
platforms :windows, :jruby do
  gem 'tzinfo', '>= 1', '< 3'
  gem 'tzinfo-data'
end

# Performance booster for watching directories on Windows
gem 'wdm', '~> 0.1', platforms: [:windows]
