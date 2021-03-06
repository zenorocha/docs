Polymer docs are mostly in Markdown with some HTML. [Jekyll][jekyll] is used to generate the static HTML for the site. The output is generated into a folder called `_site` and served from Google App Engine.

## Prereqs and installation requirements

We use Jekyll 1.4.2+ and [Grunt][grunt] to generate the documentation. You'll need to install the requirements before working on the docs (these instructions assume [NPM is already installed](http://nodejs.org/download/)):

    gem install jekyll
    gem install kramdown
    gem install jekyll-page-hooks
    npm install -g grunt-cli

**Note:** If you receive permission warnings, you may need to run the above tasks with `sudo`.

You'll also need the App Engine SDK to run the dev_appserver and preview the docs locally. [Download the SDK](https://developers.google.com/appengine/downloads).

### Check out the documentation

Checkout this repo:

    git clone https://github.com/Polymer/docs.git --recursive

Run the setup script:

    cd docs
    ./scripts/setup.sh

This will run `npm install`, pull down any external dependencies, and kickoff the `grunt docs` task. **Note:** these scripts can take some time to install.

During the setup process the `polymer-all/projects` directory is populated for you. Whenever the site is released you'll need to re-run `polymer-all/tools/bin/pull-all-projects.sh` in order to refresh this directory. See the [Polymer release](#polymer-release) section for more details.

## Making edits and previewing changes

This repo (`Polymer/docs`) is where the documentation source files live. To make a change:

1. First, fire up the App Engine dev server in this folder (`dev_appserver.py .`) to preview the docs. There's also a grunt task that starts the serve (`grunt serve`).
1. To build the docs, in another terminal, run `grunt` in base of the docs diretory. This starts up jekyll and watches for changes as you make edits. Be sure to run `npm install` in your docs directory if it's a new checkout. It can take some time for the docs to fully regenerate and be copied to the output folder...keep refreshing!
1. Make your edits.

Jekyll generates the static site in a folder named `_site`. **Note**: If you're not running jekyll to rebuild the site, you won't see your changes in the dev server.

Once your changes look good, `git commit` them and push.

## Releases: pushing the docs

**Note**: only project owners can publish the documentation.

### Preview locally

It's a good idea to run `grunt docs` before pushing the docs, as it runs a number of grunt tasks. Verify things went well and preview your changes locally using the dev server.

### Release

When we push a new version of Polymer, the site should be updated to use it. In addition,
the element reference and other projects will need updating.

To update polymer.js, the poyfills, components, projects, etc., run the following in the root of the docs directory:

    ./scripts/release.sh

Once these are updated, you need to update some versions for the docs:

- Increment the version in `app.yaml`;
- Update the Polymer release version in `_config.yml`.
- Add a link point link to the release notes in `changelog.md`.

Build the docs:

    grunt docs
    
At this point, run the dev server and preview things locally to make sure nothing is terribly
broken after Polymer and the elements have been updated. 

Next, run the deploy script in the root of the `Polymer/docs` directory:

    ./scripts/deploy_site.sh
    
This script builds the site, api docs, runs Vulcanizer over the imports, and deploys to App Engine.    

Last thing is to switch the app version in the App Engine admin console. To make the docs live, hit up https://appengine.google.com/deployment?&app_id=s~polymer-project and select the version you just deployed.

[jekyll]: http://jekyllrb.com/
[grunt]: http://gruntjs.com/
