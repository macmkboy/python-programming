# Codelabs Site

A site for hosting codelabs content.

## Prerequisites

The project requires the following major dependencies:

- [Go](https://golang.org/dl/) language
- [Node.js](https://nodejs.org/en/download/) v10+ and [npm](https://www.npmjs.com/get-npm)
- [gsutil](https://cloud.google.com/storage/docs/gsutil_install)
- [claat](https://github.com/googlecodelabs/tools/tree/master/claat#install)

With Node installed, run `npm install` in the root of the `site` (this folder):

```text
$ npm install
```

(Optional) npm installs binstubs at `./node_modules/.bin/`. When running tasks
like `gulp`, you may want to add `./node_modules/.bin/` to your `$PATH` for convenience:

```text
$ export PATH="./node_modules/.bin/:$PATH"
```

This does pose a mild security risk, so please do so at your own risk.


## Development

### Serve

To serve the site in dev mode, run `gulp serve`, passing a path to a directory
with the codelabs content as an argument. This will compile all the views and
codelabs into the `build/` directory and start a web server to serve that
content.

```text
$ gulp serve
```

The output will include the address where the server is running
(http://localhost:8000 by default).

You can also serve the completely compiled and minified (prod) version with the
`serve:dist` command. Always run this before publishing, as it will show you an
replica of what will appear on staging/production.

```text
$ gulp serve:dist
```

### Views

Views are themed/styled collections of codelabs, served from the same url.
Codelab tags are used for selecting codelabs into views. View definitions are
stored in `/app/views` directory. Views commonly correspond to events (e.g. NEXT
2018) or abstract themes (e.g. Windows), but we refer to them generically as
"views" internally.

#### Add a new view

To add a new view (e.g. to serve codelabs for a particular event):

1. Create a new folder in `app/views`, which will be the "view id". As the view
id will appear in the URL, the name should be sluggified, meaning all lowercase
with special characters replaced with dashes.

    ```text
    // General example
    My Codelab -> my-codelab

    // Always substitute file extensions, otherwise the browser will get confused
    Learn underscore.js -> learn-underscore-js

    // Keep other URL-friendly characters when the context warrants
    Tracking with utm_source -> tracking-with-utm_source
    ```

1. Add a new file named `view.json` inside this folder. Here is a template
`view.json`:

    ```javascript
    // app/views/my-event/view.json
    {
      // Required: page and view title.
      "title": "My Event",

      // Required: Text description of the event. This will appear in the view
      // landing page.
      "description": "",

      // Required: list of string tags by which to include codelabs. Tags are
      // specified in the codelab by the codelab author.
      "tags": [],

      // Optional: list of string categories by which to include codelabs.
      // Categories are specified in the codelab by the codelab author.
      "categories": [],

      // Optional: name of a custom stylesheet to include. See also: point below
      // about custom styles.
      "customStyle": "style.css",

      // Optional: list of regular expressions to exclude particular codelabs.
      "exclude": [],

      // Optional: URL to a custom logo for the codelab. If provided, this logo
      // must be placed in app/views/<view-id>/ and referenced as such. For
      // example, if the event was named "my-event", this logo would exist at
      // app/views/my-event/my-event-logo.svg.
      //
      // Where possible, please use SVG logos. When SVG logos are not available,
      // please size images to be 125px high at 72 DPI.
      //
      // Minify images before uploading using a tool like ImageOptim.
      "logoUrl": "/my-event/my-event-logo.svg",

      // Optional: category level to use for iconography
      "catLevel": 0,

      // Optional: Method for sorting codelabs.
      "sort": "mainCategory",

      // Optional: List of codelab IDs that should be "pinned" at the start.
      // This is useful for "getting started" codelabs or when users should
      // complete codelabs in a specific order.
      "pins": [],

      // Optional: custom google analytics tracking code. By default, all
      // codelab views are tracked on the main codelab analytics.
      "ga": "",

      // Optional: If true, do not include this view in the list of views on
      // the home page. It will still be accessible via the direct URL.
      "hidden": false,
    }
    ```

1. (Optional) Add a file named `style.css` inside the view folder. If provided,
this file will be included in the HTML, allowing for custom styles.

    This file is not included in the main assets bundle, so there will be a
    performance decrease as the browser needs to load additional styles.
    Furthermore, if the codelab schema were to change, your custom styles may be
    broken. Please check with the codelabs team to see if your style changes
    make more sense to upstream across all views.

1. (Optional) Add a file named `index.html` inside the view folder. This allows
you to fully customize the view, but comes at the expense of duplication. Please
use this sparingly and check with the core team before making drastic changes.

1. Execute the `serve` command as described above to see the view.

#### Build and serve a single view (deprecated)

The build is very fast, so you should not need to filter by a specific view.
Instead, build all views and then visit the appropriate URL. If you still wish
to build a single set of views, you can do so with the `--views-filter`
parameter:

```text
$ gulp serve --views-filer='^event-*'
```

Note this filter takes a regular expression. By default, all views are built.


## Deployment (notes to self)

We will deploy to Github pages here so make sure you have a gh-pages branch of the repo you are deploying to.

You will need to update the baseUrl in `gulpfile.js` as well as `app/scripts/app.js`. It's unfortunate that it has to be done here but a workaround is TBC.

Fix the repo name in the last few lines of `gulpfile.js` then run `gulp deploy:prod`. 

There may be a cryptic error about the `.publish` directory. Worksround is to checkout `gh-pages`, copy .git folder outside repo, checkout `master`, move `gh-pages` git repo to .publish. Can't figure out a better way yet... 

*NB:* The command `gulp deploy:build` is broken at the moment. Always use `gulp deploy:prod`.


## Help

For help documentation/usage of the Gulp tasks, run:

```text
$ gulp -T
```

If gulp startup times are really slow, try removing `node_modules/` or running

```text
$ npm dedupe
```