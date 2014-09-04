jekyll-static-version-tag
=========================

Liquid tag for adding version numbers to [Sass](http://sass-lang.com/) and Javascript file includes for a [Jekyll](http://jekyllrb.com/) site per the recommendation of [HTML5 Boilerplate](http://html5boilerplate.com/).

This tag generates a version number based on the last modified time of a javascript or scss file (and its included partials) and inserts it into the appropriate `<script>` or `<link>` tag.  For instance, for a Sass file named `main.scss` it will generate a `<link>` pointing to `main.20140904121212.css`, where '20140904121212' is the last modified time of the `main.scss` file on the file system.  

## Usage

```liquid
{% include_scss main %}
{% include_js main %}
```

Will return:

```html
<link rel="stylesheet" href="/css/main.20140904111838.css">
<script defer src="/js/main.20140903232620.js"></script>
```

This does not actually create a file named `main.20140903232620.js` in the Jekyll build process, instead it uses mod_rewrite on apache to redirect all requests for `main.*.js` to `main.js`.  This allows the browser to cache the file for essentially forever (1 year), because a change to the file from the web developer's perspective will result in a new version, which will prompt a re-download.   

## Installation

1. Copy `jekyll-static-version-tag.rb` to your `_plugins` directory.
2. Add the following to your `.htaccess` or apache conf:

```apache
<IfModule mod_expires.c>

    ExpiresActive on
    ExpiresDefault                                      "access plus 1 month"

  # CSS
    ExpiresByType text/css                              "access plus 1 year"

  # JavaScript
    ExpiresByType application/javascript                "access plus 1 year"
    
  # Other filetypes go here...
    
</IfModule>

<IfModule mod_rewrite.c>
   RewriteCond %{REQUEST_FILENAME} !-f
   RewriteRule ^(.+)\.(\d+)\.(js|css|png|jpe?g|gif)$ $1.$3 [L]
</IfModule>
```

For more information on these settings, see [the `.htaccess` file from the HTML5 Boilerplate project](https://github.com/h5bp/html5-boilerplate/blob/master/dist/.htaccess).  

## Credits

This tag is very loosely based on the source code in this article [Blogging With Jekyll, Haml, Sass, and Jammit](http://mikeferrier.com/2011/04/29/blogging-with-jekyll-haml-sass-and-jammit/) by Mike Ferrier.  
