---
title: Using Ruby In CI/CD with Codeship Basic
shortTitle: Ruby
menus:
  basic/languages:
    title: Ruby
    weight: 1
tags:
  - ruby
  - languages
  - rails
  - sinatra
categories:
  - Languages And Frameworks
redirect_from:
  - /languages/ruby/
  - /general/projects/could-not-find-gemname-in-any-of-the-sources/
---

* include a table of contents
{:toc}

## Versions And Setup

### RVM
We use RVM to manage different Ruby versions. We set <strong>{{ site.data.basic.defaults.ruby }}</strong> as the default version. Currently we do not load the Ruby version from your Gemfile. You can always change the Ruby version by running:

```shell
rvm use RUBY_VERSION_YOU_WANT_TO_USE
```

The following Ruby versions are preinstalled

{% include basic/ami/{{ site.data.basic.ami_id }}/ruby.md %}

### Using a .ruby-version file
You can also use your .ruby-version file on Codeship. The .ruby-version file lives in the project root and its content is just your Ruby version, for example: `2.0.0-p195`. You can just read the ruby version to use from that file:

```shell
rvm use $(cat .ruby-version) --install
```

One use case is that you can change your Ruby version for different branches.

## Dependencies

You can install dependencies using bundler in your [setup commands]({{ site.baseurl }}{% link _basic/quickstart/getting-started.md %}).

For example:

```shell
gem install bundler
```

If you want to use a version of bundler different than what is preinstalled on the VM, you will need to install bundler yourself by adding the following command to your setup steps

### Dependency Cache

Codeship automatically configures bundler to use the `$HOME/cache/bundler` directory, which we save between builds to optimize build performance. You can [read this article to learn more]({{ site.baseurl }}{% link _basic/builds-and-configuration/dependency-cache.md %}) about the dependency cache and how to clear it.

## Frameworks And Testing

Our Ruby support includes Ruby itself, Rails, Sinatra and most other frameworks that do not require root-access for customized system configuration.

We also support all Ruby based test frameworks from RSpec, Cucumber to Minitest or others.

Capybara is also supported out of the box with the selenium-webdriver , capybara-webkit or the poltergeist driver for phantomjs.

## Parallel Testing

If you are running [parallel test pipelines]({{ site.baseurl }}{% link _basic/builds-and-configuration/parallel-tests.md %}), you will want separate your RSpec tests into groups and call a group specifically in each pipeline. For instance:

**Pipeline 1**:
```shell
rspec spec/spec_1
```

**Pipeline 2**:
```shell
rspec spec/spec_2
```

### Parallelization Gems

In addition to parallelizing your tests explicitly [via parallel pipelines]({{ site.baseurl }}{% link _basic/builds-and-configuration/parallel-tests.md %}), there are a couple Rails gems that are popular ways to parallelize within your codebase.

While we do not officially support or integrate with these modules, many Codeship users find success speeding their tests up by using them. Note that it is possible for these gems to cause resource and build failure issues, as well.

- [https://github.com/grosser/parallel_tests](https://github.com/grosser/parallel_tests)
- [https://github.com/ArturT/knapsack](https://github.com/ArturT/knapsack)


## Notes And Known Issues

### Nokogiri
On **Ruby 2.3 only**, Nokogiri will fail to compile with the bundled _libxml_ and _libxslt_ libraries. To install the gem you need to use the system libraries instead.

```shell
# add the following command before running "bundle install"
bundle config build.nokogiri --use-system-libraries
```

### Run With Bundle Exec

Make sure to run your commands with `bundle exec` (e.g. `bundle exec rspec`) so all commands you run are executed with the versions of the ruby gems you configured in your Gemfile.lock

### Can Not Find Gem In Sources

Sometimes you might see errors like the following:

```
Could not find safe_yaml-0.9.2 in any of the sources
```

Please make sure that the version of the gem you want to install there wasn't yanked from [Rubygems](http://rubygems.org/)
