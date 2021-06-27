---
  layout: post
  title: Revive old Jekyll theme to version 4.2 running on macOS Big Sur with Apple Silicon
  featured: true
  author: john
  tags: [python, ruby, java, jekyll, disqus]
  image: '/images/posts/hello-world.png'
---

Many thanks for reaching guys. I was trying to use this old Jekyll theme [East by Aspire Themes](https://aspirethemes.com/docs/east-jekyll) to set up a brand new blog, ideally tiny with integration of #disqus comments, can be hosted on FREE Github Pages. And I came across this theme package via [Envato](https://elements.envato.com/east-minimal-and-clean-jekyll-blog-theme-SJ9C94). 

![alt text](/images/posts/East-Jekyll-Envato.png "East for Jekyll")

According to the git community, applying any theme to your precious Jekyll blog can be a royal pain. So is this one.

The issue with this theme package is that it was developed back in 2018 (still, I paid for it. [buy at your own risk](https://elements.envato.com/east-minimal-and-clean-jekyll-blog-theme-SJ9C94)) and there wasn't a Ver 4.2 Jekyll thing what-so-ever at the time, let alone a bounch of OLD depandencies required for supporting this theme running. 

Tracking it back to the developer, the Aspire Themes, it appears that they have now completely abandanted the Jekyll line for Ghost. For instance the "East" theme package is only [available](https://aspirethemes.com/themes/east) for Ghost users amoung their products. I could not find a single mentioning of Jekyll. 

Should I gave up? No. I wanted something simple simple and allowing me to write markdowns to publish, This appeared to be the one.

Although a genuine newbie of Ruby/Jekyll, I was lucky enough to bring this 3-years old theme back to life for Jekyll Ver 4.2 ONLY after a few debugging and trouble shotting, I mean googling, stack-overflow, dev.io, official Jekyll community (credit to [michaelcurrin](https://talk.jekyllrb.com/t/installing-themes-requires-install-of-outdated-gems/3486/3)), etc. Here it is.

#### The solution
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
##### Original discussion that helped
- https://talk.jekyllrb.com/t/installing-themes-requires-install-of-outdated-gems/3486/3

##### Publish it to Github Pages
- https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll