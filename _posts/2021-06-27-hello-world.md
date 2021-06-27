---
  layout: post
  title: Revive old Jekyll theme in version 4.2 runing on macOS Big Sur with Apple Silicon
  featured: true
  author: john
  tags: [python, ruby, java]
  image: '/images/posts/hello-world.png'
---

Many thanks for watching guys. I am using this theme [East by Aspire Themes](https://aspirethemes.com/docs/east-jekyll)

The make it running,
1. delete Gemfile.lock 
try 
```
bundle install
```
get
```
Deprecation: You appear to have pagination turned on, but you haven't included the `jekyll-paginate` gem. Ensure you have `plugins: [jekyll-paginate]` in your configuration file.
```
2. fix the deprecated pagination issue, will look into again in the future if it runs into further issue
2.1 add in _config.yml file with
```
# don't need to as the original config included this
gems:
  - jekyll-paginate
paginate: 10
```
2.2 add in Gemfile file with
```
group :jekyll_plugins do
   gem "jekyll-paginate"
end
```
3. in Terminal try:
```
bundle install
```
got following. p.s. I am in a newly setup macOS on Apple Silicon
```
/Users/xxxxx/.gem/ruby/3.0.0/gems/jekyll-4.2.0/lib/jekyll/commands/serve/servlet.rb:3:in `require': cannot load such file -- webrick (LoadError)
```
4. add webrick to bundle (in this theme build?)
```
bundle add webrick
```
5. finally rebuild with success! 
```
bundle install
bundle exec jekyll serve
```
Enjoy!

#### Reference
- https://talk.jekyllrb.com/t/installing-themes-requires-install-of-outdated-gems/3486/3

#### Show me some git commit
```
# This update is made via git and published at Github Pages
```