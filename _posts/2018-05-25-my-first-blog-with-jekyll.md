---
layout: post
title:  "My First Blog with Jekyll on GitHub, How to ?"
date:   2018-05-25 16:16:01 +0700
categories: [jekyll]
---
### Creating GitHub Pages
First, Create a repository on GitHub by following the steps in
[Getting Started with GitHub Pages](https://guides.github.com/features/pages/).
This guide is to help you create a repository step by step.

### Setting up your GitHub Pages site Locally
Set up a local version of your Jekyll GitHub Pages site to test changes.
Following instructions in [Setting up your GitHub Pages site Locally with Jekyll](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/)
to set up site locally.

### Creating `.gitignore` file
The `.gitignore` file will looks like:

```
_site/
# ignore composer vendor directory
/vendor
.bundle/
.sass-cache/
.jekyll-cache/
.jekyll-metadata
Gemfile.lock
```

### Customizing Theme
I use the Cayman theme. If you'd like to customize the theme's HTML. Following
the [The Cayman theme page](https://github.com/pages-themes/cayman).

### Markdown Code Blocks in Jekyll
Jekyll uses the Liquid templating language to process templates.
If you need to write **Liquid template code**  in your markdown file.
You came with the problem with the double curly brackets.
Thank [Writing Liquid Template in Markdown Code Blocks with Jekyll](http://ozzieliu.com/2016/04/26/writing-liquid-template-in-markdown-with-jekyll/) blog for solution.
> One option is to use Liquid’s {% raw %} {% raw %} {% endraw %}tag.
> Raw temporarily disables tag processing until the {% raw %} \{% endraw %\} {% endraw %}.

{% raw %}
~~~~~~~~~~~~~~~~~~~
{% raw %}
~~~
code here
~~~
{% endraw % }
~~~~~~~~~~~~~~~~~~~
{% endraw %}


### Customizing My Layouts
* Create a folder called `_layouts` in the site's root folder.
* Copy the `default.html` from the [GitHub theme's repository](https://github.com/pages-themes/) to the `_layouts` folder.
* Create the `blog.html` in the `_layouts` folder. (Blog template layout). I copy this from

{% raw %}
~~~html
---
layout: default
---
<section class="blog">
    {{ content }}
    <ul class="list">
    {% for post in site.posts %}
        <li>
            <time class="time">
                {% assign date_format = site.date_format | default: "%-d %b, %Y" %}
                {{ post.date | date: date_format }}
            </time>
            <h2>
                <a class="link" href="{{ post.url | relative_url }}" role="link">{{ post.title | escape }}</a>
            </h2>
            <p class="meta">
                {{ post.content | strip_html | escape | truncatewords: 80 }}
            </p>
        </li>
    {% endfor %}
    </ul>
</section>
~~~
{% endraw %}

* Create the `post.html` in the `_layouts` folder. (Post template layout). List contents of `_layouts` folder in a tree-like format.

```
$ tree _layouts/
_layouts/
├── blog.html
├── default.html
└── post.html

0 directories, 3 files
$
```

### Writing Posts
Following the [Writing posts](https://jekyllrb.com/docs/posts/) page in [Jekyll](https://jekyllrb.com/) website to create your first blog post.

```
$ tree _posts/
_posts/
└── 2018-05-25-my-first-blog-with-jekyll.md

0 directories, 1 file
```
### Run your Jekyll site locally

```
$ bundle exec jekyll serve
```

This is my first blog post screenshot:

![My Posts screenshot]({{ "/assets/img/blog/Screenshot from 2018-05-26 17-34-13.png" | absolute_url }})

### Show GitHub Repository Metadata
Append the `_config.yml` with this content.

```
email: your email
github:
  is_project_page: true
  repository_url: your_github_repository_url
  repository_name: your_github_repository_name
  owner_url: your_github_url
  owner_name: your_name
```
Screenshot show GitHub Repository Metadata after edit the `_config.yml`.

![Show GitHub Repository Metadata]({{ "/assets/img/blog/Screenshot from 2018-05-26 21-06-34.png" | absolute_url }})

### Enabling Google Analytics
If you are using [The Cayman theme](https://github.com/pages-themes/cayman) with Jekyll. To enable Google Analytics, append the following lines to `_config.yml`:

```
# Google Analytics
google_analytics: UA-123456789-0
```

The `UA-123456789-0` is your [**Google Analytics Tracking ID**](https://support.google.com/analytics/answer/1008080?hl=en). The Cayman theme will auto enable Google Analytics with script in the `default.html`

{% raw %}
~~~html
{% if site.google_analytics %}
  <script>
    (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
    (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
    m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
    })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

    ga('create', '{{ site.google_analytics }}', 'auto');
    ga('send', 'pageview');
  </script>
{% endif %}
~~~
{% endraw %}

### [Controlling URLs and Links in Jekyll](https://www.digitalocean.com/community/tutorials/controlling-urls-and-links-in-jekyll)
