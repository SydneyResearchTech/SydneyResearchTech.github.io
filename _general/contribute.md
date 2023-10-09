---
title: Contributing to documentation
draft: false
---

## Add MarkDown to existing collections

To add new pages to an existing collection, add a MarkDown document into the appropriate collection directory. Ensure that the following basic front matter is applied.

When page is ready to be published alter the draft tag to `true`.

```MarkDown
---
title: ADD APPROPRIATE PAGE TITLE
draft: true
authors: []
---

ADD MARKDOWN CONTENT
...

```

## Run Jekyll server locally for complex contributions

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
