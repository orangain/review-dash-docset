review-dash-docset
==================

[Re:VIEW](https://github.com/kmuto/review) docset for [Dash](https://kapeli.com/dash).

![Screenshot of Re:VIEW Docset](https://raw.githubusercontent.com/orangain/review-dash-docset/master/screenshot.png)

How to use the docset
---------------------

1. Open Preferences > Downloads > User Contributed.
2. Search the Re:VIEW docset.
3. Download it.

How to build
------------

### Prerequisites

* Ruby 2.0+
* Bundler

### Prepare

```
$ git clone https://github.com/orangain/review-dash-docset
$ bundle install --path=vendor/bundle
```

### Generate docset

```
$ bundle exec rake
```

`ReVIEW.docset` will be generated.


References
----------

* [review/format.md at master · kmuto/review](https://github.com/kmuto/review/blob/master/doc/format.md#referring-headings)
* [Docset Generation Guide - Kapeli](https://kapeli.com/docsets)
* [Markdown | GitHub API](https://developer.github.com/v3/markdown/)
* [sindresorhus/github-markdown-css](https://github.com/sindresorhus/github-markdown-css)


License
-------

LGPL 2.1, Copyright (c) 2015 orangain <orangain@gmail.com> unless otherwise specified.
See LICENSE file.
