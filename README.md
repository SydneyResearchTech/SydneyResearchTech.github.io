# SydneyResearchTech.github.io

* https://pages.github.com/versions/

```bash
# Install ruby v2.7.4
brew install asdf
echo '. $(brew --prefix asdf)/libexec/asdf.sh' >>$HOME/.zshrc
asdf plugin add ruby https://github.com/asdf-vm/asdf-ruby.git
asdf install ruby 
bundle install
```

```bash
# OS X M1 (ARM64)
gem install eventmachine -v '1.2.7' --source 'https://rubygems.org/' -- --with-openssl-dir=$(brew --prefix libressl)
```
