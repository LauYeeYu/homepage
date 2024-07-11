# Homepage

This is the source code for the [my personal website][lau-yeeyu-org].

[lau-yeeyu-org]: https://lau.yeeyu.org

## How to Use and Deploy

This project uses Hugo to generate the static website. To build the website,
run:

```sh
hugo
```

Please note that you should change the `googleAnalytics` field in the
`hugo.toml` file to your own Google Analytics ID or remove that line before
you deploy the website.

To just start a local server, run (this will disable the google analytics):

```sh
hugo server
```

For more information about the usage and deployment, as well as how to create
and customize your own website, you may check the blog post
[here][set-up-website-with-hugo-and-vercel].

[set-up-website-with-hugo-and-vercel]: https://lau.yeeyu.org/blog/set-up-my-personal-site-with-hugo-and-vercel/

## License

Unless otherwise stated, all rights reserved for the contents in `content/`
and `static/` directories.

For other files, they are licensed under the MIT License. See the
[LICENSE](LICENSE) file for details.
