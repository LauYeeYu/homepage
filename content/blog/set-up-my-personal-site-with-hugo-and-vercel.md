+++
title = 'Set up my Personal Site with Hugo and Vercel'
date = 2024-05-08T19:00:00+08:00
draft = false
tags = ['hugo', 'vercel', 'web']
+++

Recently, I create my personal site with [Hugo][hugo], and deploy it with [Vercel][vercel]. Here are the steps I took to set up my personal site.

[hugo]: https://gohugo.io/
[vercel]: https://vercel.com/

<!--more-->

## Create a Site

### Create an Empty Site

With Hugo installed, creating a new site is really easy. Simply run the
following command:

```bash
hugo new site <dir>
```

This will create a new Hugo site in the specified directory. Then set up a
git repository for the site.

```bash
cd <dir>
git init
```

### Add a Theme

A lot of [themes][hugo-themes] are provided by the Hugo community. You can
choose one that you like and add it to your site. I use the [soho theme][soho]
for my site.

[hugo-themes]: https://themes.gohugo.io/
[soho]: https://themes.gohugo.io/themes/soho/

### Tailor the Theme to you own Purpose

Using a theme as a base is a good way to get started, but it is not enough.
Every site has its own purpose, every person has its own style. It is
important to change the setting to fit your own purpose.

For me, I don't want my posts to be shown on the homepage. But the soho theme
shows all posts on the homepage by default. So I need to change the index
page.

Also, I don't want my theme to be the same as others. So picking a new theme
color and making some modification on the css is necessary.

Therefore, it is important to have a new theme derived from the original
theme. To achieve this, you can fork the original theme, and add your own
theme as a submodule to your site. You may use the following command to add
the submodule:

```bash
git submodule add <url> themes/<theme-name>
```

Changing the index page is really easy. Hugo puts the layout files in the
`layouts` directory. You can change the `layouts/index.html` file.

As for modifying the css, the css files are in the `static` directory. Find
the css file you want to modify in your theme directory, and change it. Then
commit the changes to the submodule and push it to the remote repository.
After that, add the modification of the submodule to the main repository.
Your may use the following command to add the modification of the submodule:

```bash
# Commit the changes of the submodule to the theme repo
cd <dir>/themes/<theme-name>
git add static/
git commit
git push
# Commit the modification to the main repo
cd ..
git add themes/<theme-name>
git commit
git push
```

### Test your Site

Hugo provides you with an easy way to test your site. You can run the
following command to start a local server:

```bash
hugo server
```

After running this command, you can open your browser and visit. The website
will keep updating when you change the content of the site. If you change
the css and the layout, it is recommended to remove the `public` directory
and run the command.

### Change the Configuration

The configuration file of Hugo is `hugo.toml`, `hugo.yaml`, or `hugo.json`.
You can change the title, the base url, the theme, and other parameters in
the configuration file.

Remember to [test your site](#test-your-site) after changing the
configuration file.

Note: If you want to use my configuration file, you can should change the
`googleAnalytics` field to your own google analytics id or remove that
line if you don't want to use it at this moment.

### Add some other Content

For example, you can add a new post with the following command:

```bash
hugo new post/<post-name>.md
```

Then you can edit the post with your favorite editor. Remember to change the
draft field to false if you want to publish the post.
[Test your site](#test-your-site) after adding the post.

The layout of the post list or other content lists are controlled by
`layouts/_default/list.html`, while the layout of posts are controlled by
`layouts/_default/single.html`. You can change the layout of the posts or
other content lists by changing these files. I'm not sure whether the layout
file are correct for all themes. If it does not work, you can change the
file according to the [Hugo documentation][hugo-list-documentation].

[hugo-list-documentation]: https://gohugo.io/templates/lists/

## Deploy the Site with Vercel

Vercel is a platform that lets you build, scale, and secure web apps with
your favorite tools and frameworks.

Deploying a Hugo site with Vercel is relatively easy, as opposed to
deploying the site with a traditional web server.

### Add a Vercel Project

After you have created a Vercel account, you can add a new project with the
vercel website. You should add your repository to the project (It is not
recommended to give the permission of all repositories, because vercel will
then have write permission on all of your repositories). Select the
repository of your site, and choose hugo as the framework. This should be
enough to deploy your site.

Then you should check the result of the deployment. If there is any error,
you can check the log of the deployment to find the error. For me, when I
deployed my site, some problems occurred.

If there is no error, you can visit the site with the url provided by vercel.

#### Problem I Encountered

The main problem is that hugo support is not that good. With default settings,
I met with the error of `hugo not found`. This is because vercel's `Node 20.x`
image does not have hugo installed. In
[this discussion][discussion-default-hugo-20], it is suggested that I should
use the `Node 18.x` image. However, with the `Node 18.x` image, the hugo
version is too old to support my site. Fortunately, vercel allows you to
specify the hugo version. Hence, by specifying the hugo version in the vercel
configuration file (`vercel.json` in the root directory of the site), I
finally fix the problem. You may see [my vercel config][my-vercel-config]
for more details.

[discussion-default-hugo-20]: https://github.com/orgs/vercel/discussions/6472
[my-vercel-config]: https://github.com/LauYeeYu/homepage/blob/master/vercel.json

### Add a Domain

If you have a domain name like me, you can add the domain name to the vercel in
domains section of the project settings. Then add a CNAME to your domain
provider to point to the vercel domain.

## Conclusion

Setting up a personal site with Hugo and Vercel is really easy, despite some
minor problems. I hope this post can help you to set up your own site. For
more details about the changes I made to the site, see the
[commit history][commit-history] of my site.

[commit-history]: https://github.com/LauYeeYu/homepage/commits/master/
