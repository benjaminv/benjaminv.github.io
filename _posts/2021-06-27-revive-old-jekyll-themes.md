---
  layout: post
  title: Revive old Jekyll theme to version 4.2 running on M1
  featured: true
  author: ben
  tags: [python, ruby, java, jekyll, disqus]
  image: '/images/posts/install-outdated-jekyll-theme.png'
---

Many thanks for reaching guys. I was trying to use this old Jekyll theme [East by Aspire Themes](https://aspirethemes.com/docs/east-jekyll) to set up a brand new blog, ideally tiny with integration of #disqus comments, can be hosted on FREE Github Pages. And I came across this theme package via [Envato](https://elements.envato.com/east-minimal-and-clean-jekyll-blog-theme-SJ9C94). 

![East for Jekyll](/images/posts/East-Jekyll-Envato.png "East for Jekyll")

According to the git community, applying any theme to your precious Jekyll blog can be a royal pain. So is this one.

The issue with this theme package is that it was developed back in 2018 (still, I paid for it. [buy at your own risk](https://elements.envato.com/east-minimal-and-clean-jekyll-blog-theme-SJ9C94)) and there wasn't a Ver 4.2 Jekyll thing what-so-ever at the time, let alone a bounch of OLD depandencies required for supporting this theme running. 

Tracking it back to the developer, the Aspire Themes, it appears that they have now completely abandoned the Jekyll line. For instance the "East" theme package is only [available](https://aspirethemes.com/themes/east) for Ghost users amoung their products. I could not find a single mentioning of Jekyll across their website. 

Time to give up? No. I wanted something simple and allowing me to write markdowns to publish, this looks like the one.

Although as a genuine newbie of Ruby/Jekyll, I was lucky enough to bring this 3-years old theme back to life for Jekyll Ver 4.2 ONLY after a few debugs and trouble-shotting, I mean googling, stack-overflow, dev.io, official Jekyll community (credit to [michaelcurrin](https://talk.jekyllrb.com/t/installing-themes-requires-install-of-outdated-gems/3486/3)), etc. Here it is.

## 1. The solution
### 1.1. delete Gemfile.lock 
try 
```ruby
bundle install
```
get
```ruby
Deprecation: You appear to have pagination turned on, but you haven't included the `jekyll-paginate` gem. Ensure you have `plugins: [jekyll-paginate]` in your configuration file.
```
### 1.2. fix the deprecated pagination issue  
p.s. will look into again in the future if it runs into some issues  

#### 1.2.1. add in _config.yml file with  
```ruby
# don't need to as the original config included this
gems:
- jekyll-paginate
paginate: 10
```

#### 1.2.2. add in Gemfile file with
```ruby
group :jekyll_plugins do
gem "jekyll-paginate"
end
```

### 1.3. in Terminal try:
```ruby
bundle install
```
got following,  
p.s. I am in a newly setup macOS on Apple Silicon
```ruby
/Users/xxxxx/.gem/ruby/3.0.0/gems/jekyll-4.2.0/lib/jekyll/commands/serve/servlet.rb:3:in `require': cannot load such file -- webrick (LoadError)
```
### 1.4. add webrick to bundle (in this theme build?)
```ruby
bundle add webrick
```
### 1.5. finally rebuild with success! 
```ruby
bundle install
bundle exec jekyll serve
```
Enjoy!  
![This blog](/images/posts/ben-it-blog.png "Ben's IT Diary")


## 2. Reference
### 2.1. Original discussion that helped
- https://talk.jekyllrb.com/t/installing-themes-requires-install-of-outdated-gems/3486/3

### 2.2. Publish it to Github Pages
- https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll

-----
__trick time__
- Q: How to find and replace every nth occurance in vscode?
> for example add a `ruby` hightlight for all code-blocks in this blog by adding a `ruby` tag following the beginning [```ruby]
- A: It takes three steps,
1. enable `regex` 
2. find `(```\n)(.*\n```)`
3. replace with `(```ruby\n$2)`