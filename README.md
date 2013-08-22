# elgalu's octopress fork

## Branches info

| Branch | Created by | Description |
|:-------|:-----------|:----------- |
| config | elgalu  | Blog configuration-related stuff, some code related stuff leaked here too.
| justin-kelly-theme | elgalu | Theme related code. The theme i chose.
| posts  | elgalu  | My Post. Where i actually write blog content.
| 2.5    | imathis | Octopress core version my blog was based on but no longer works upgrading due to plugins deprecations.

## Regenerate & Deploy

Regenerate & Deploy

    bundle exec rake generate

Preview in browser

    bundle exec rake preview

Deploy to github

    bundle exec rake deploy

Note every time you clone the repo you will need to again do:

    bundle exec rake setup_github_pages

Note after that commit you may need to merge gh-pages into master on your {username}.github.io repository