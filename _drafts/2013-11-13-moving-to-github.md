---
title: Moving to GitHub
date: 2013-11-13
tags:
  - admin
---

This blog has been hosted on [scriptgram][] for the past year or so.
Unfortunately, while I like the publish-via-Dropbox mechanism, there
have been enough problems recently that I've finally switched over to
using [GitHub Pages][] for hosting.  I've been thinking about doing
this for a while, but the things that finally pushed me to make the
change were:

- Sync problems that would prevent new posts from appearing (and that
  at least once caused posts to disappear).
- Lack of any response to bug reports by the site maintainers.

Fortunately, [GitHub Pages][] is also a Markdown-based solution.
GitHub uses [Jekyll][] to render Markdown to HTML.  The conversion was
generally easy, although in order to preserve links pointing at the
old blog I need to generate a bunch of HTML redirect files. I
automated that process with the following script:

    #!/bin/sh

    for post in _posts/*; do
      title=$(grep '^title:' $post)
      title=${title/title: /}

      slug=${title,,}
      slug=${slug// /-}
      slug=${slug//[.,:?\/\'\"]/}

      post_name=${post/_posts\//}
      post_date=${post_name:0:10}
      post_title=${post_name:11}
      post_title=${post_title:0:$(( ${#post_title} - 3))}

      post_year=${post_date%%-*}
      tmp=${post_date#*-}
      post_month=${tmp%%-*}
      post_day=${post_date##*-}

      new_url="/$post_year/$post_month/$post_day/$post_title/"
      
      mkdir -p post/$slug
      sed "s|URL|$new_url|g" redirect.html > post/$slug/index.html
    done

Where `redirect.html` looks like this:

    <!DOCTYPE html>
    <html>
    <head>
    <link rel="canonical" href="URL"/>
    <meta http-equiv="content-type" content="text/html; charset=utf-8" />
    <meta http-equiv="refresh" content="0;url=URL" />
    </head>
    </html>

[github pages]: http://pages.github.com/


