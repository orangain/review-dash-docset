review-dash-docset
==================

[Re:VIEW](https://github.com/kmuto/review) docset for [Dash](https://kapeli.com/dash).

How to install docset in Dash
-----------------------------

1. Clone this repository.
2. Double click `review.docset` to add the docset to Dash.


How to build
------------

### Requirements

* Ruby 2.0+
* Bundler

### Prepare

```
bundle install --path=vendor/bundle
```

### Generate docset

```
bundle exec rake
```

`review.docset` will be generated.


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
