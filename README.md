# elgalu's octopress fork

## Branches info

| Branch             | Created by | Description |
|:-------------------|:-----------|:----------- |
| config             | elgalu     | Blog configuration-related stuff, some code related stuff leaked here too.
| justin-kelly-theme | elgalu     | Theme related code. The theme i chose.
| posts              | elgalu     | My Post. Where i actually write blog content.
| 2.5                | imathis    | Octopress core version my blog was based on but no longer works upgrading due to plugins deprecations.

## Regenerate & Deploy

First time, after git clone:

    bundle exec rake setup_github_pages
    # enter:
    # git@github.com:elgalu/elgalu.github.io

Regenerate

    bundle exec rake generate

Preview in browser

    bundle exec rake preview

Deploy to github

    bundle exec rake deploy

Note after that commit you may need to merge gh-pages into master on your {username}.github.io repository

## Create new post

    rake new_post["Run protractor against Internet Explorer VM"]
    #=> Creating new post: source/_posts/2015-02-10-run-protractor-against-internet-explorer-vm.markdown
