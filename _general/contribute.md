---
title: Contributing to documentation
draft: false
---

## Setup

```bash
# Install asdf using brew on OS X
brew install asdf

# Install ruby with appropriate version
asdf plugin add ruby
asdf install ruby 2.7.4

# Clone repo to your local machine
git clone git@github.com:SydneyResearchTech/SydneyResearchTech.github.io.git
cd SydneyResearchTech.github.io

# Install Jekyll and plugins at specified versions
bundle install

# Run Jekyll server
bundle exec jekyll serve --draft
```
