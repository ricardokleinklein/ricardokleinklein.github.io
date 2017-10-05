# ricardokleinklein's blog 

This is the repository containing the files required to deploy a blog on Github pages. [Checkout the website](https://ricardokleinklein.github.io).

![screenshot](/images/screenshot.png)

## Contents At-A-Glance

This theme has all the necessary files and directories required to have a new Jekyll site up and running in no time, with zero-configuration.

### Layouts

Refers to the files within the `_layouts` directory, that define the markup for your theme.

* `default.html` — The base layout that lays the foundation for subsequent layouts. The derived layouts inject their contents into this file at the line that says `{{ content }}` and are linked to this file via FrontMatter declaration `layout: default`.
* `post.html` — The layout for your posts.

### Includes

Refers to snippets of code within the `_includes` directory that can be inserted in multiple layouts (and another include-file as well) within the same theme-gem.

* `head.html` -- Code-block that defines the `<head></head>` in default *layout*.
* `footer.html` -- Defines the site's footer section.
* `svg-icons.html` --  Inserts github, twitter, Facebook and other ids with respective icons.
* `analytics.html` -- Inserts Google Analytics module.
* `disqus.html` -- Code to markup disqus comment box.
* `meta.html` -- Include some meta data for searching purposes (still on progress).

### Sass

Refers to `.scss` files within the `_sass` directory that define the theme's styles.

* `_base.scss` -- Resets and defines base styles for various HTML elements.
* `_catalogue.scss` -- Sets the style for the list of posts in the index page.
* `_layout.scss` -- Defines the general style of a page.
* `_pagination.scss`-- Defines the style of the pagination elements.
* `_post.scss` -- Sets specific styles for the post pages.
* `_variables.scss` -- Defines the value of several elements used in various layouts.


### Enabling comments via Disqus

Optionally, in case you have a Disqus account, you can tell Jekyll to show a comments section below each post. To enable it, just add the following lines to your Jekyll `_config.yml` file.

`disqus: your_disqus_username`

If you don't want to display comments for a particular post you can disable them by adding `comments: false` to that post's YAML Front Matter.

## License

MIT License

Copyright (c) 2017 Ricardo Faúndez-Carrasco

Permission is hereby granted, free of charge, to any person obtaining a copy 
of this software and associated documentation files (the "Software"), to deal 
in the Software without restriction, including without limitation the rights 
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is 
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all 
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR 
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE 
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE 
SOFTWARE.