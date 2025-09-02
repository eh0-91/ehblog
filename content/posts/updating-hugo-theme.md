---
title: "Updating a Hugo theme"
date: 2025-09-02T08:10:40+02:00
draft: false
---

# Long time no see - Time to update my Hugo theme

I haven't taken a look at my blog in a long time now. So I decided to transfer the codebase
to a different GH account and repo and update it. Updating Hugo locally is easy, it's a static executable.
When building the site (either locally or with Cloudflare Pages) it shows me an error though:

```
ERROR deprecated: resources.ToCSS was deprecated in Hugo v0.128.0 and subsequently removed. Use css.Sass instead.
```

Seems like an error caused by the theme I'm using, which is supposed to be updated seperately.
I checked out the [theme](https://github.com/vaga/hugo-theme-m10c) and realized the repo has changed.
At least I assume so because as far as I remember the repo was just called "m10c" and that's how it was configured in
my Hugo project. So I decided to add the "new" repo and use it instead to hopefully fix the error.

```
git submodule add https://github.com/vaga/hugo-theme-m10c.git themes/hugo-theme-m10c
```

After adding the new repo as a submodule I updating my config.yaml to use the new theme with the following line

```
theme: "hugo-theme-m10c"
```

Last thing left is to just commit my changes and who would have thought, building the site with Cloudflare pages
is working again. Otherwise you would not be reading this post. :P I hope I'll find some more time blogging
in the foreseeable feature.
