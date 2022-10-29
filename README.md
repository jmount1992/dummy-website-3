# Creating a Jekyll Website From Scratch

The following tutorial is based on the [Jekyll's Step by Step Tutorial](https://jekyllrb.com/docs/step-by-step/01-setup/). This tutorial is to help become familiar with aspects of Jekyll. You will most likely end up using an existing Jekyll theme. However, you may wish to alter that theme or slightly repurpose the theme to your use case. It is much easier to alter or repurupose a theme to get the desired outcome if you are familiar with a few aspects.

The terminal commands are for a Ubuntu operating system, but should be able to be easily recreated in your operating system. 

## Install Ruby, Bundle and Jekyll

Before we get started make sure you have Jekyll installed. You can follow the guides provided by [Jekyll](https://jekyllrb.com/docs/installation/).

## Creating Your Website
 
1. Create a directory to hold all our files and navigate into the directory. This directory will be referred to as the `root`. You can call it whatever you want. 
    ```bash
    mkdir <my-website-folder> && cd <my-website-folder>
    ```

2. Create a Gemfile in your root directory.
    ```bash
    touch Gemfile
    ```

3. Add the following to the Gemfile. We will talk about this more later.
    ```ruby
    source "https://rubygems.org"
    gem "github-pages", "~> 220", group: :jekyll_plugins
    ```

4. Install the Gems
    ```bash
    bundle install
    ```

5. Create an `index.md` to act as the homepage.
    ```bash
    touch index.md
    ```

6. Add the following code to `index.md`. Any code between the two sets of `---` are what is called front matter. Jekyll will only process any files that contain front matter. That is why this ReadMe file gets skipped. It does not contain any front matter.
    ```markdown
    ---
    ---
    # My Website 
    Welcome to my website!
    ```

7. Locally build and serve your website. The `--livereload` will automatically rebuild and serve the website if we make any changes to any of the content files. However, if our changes result in a bad state you may need to kill and restart the process.
    ```bash
    bundle exec jekyll serve --livereload
    ```

8. Create a `_layouts` folder and create a file called `default.html`. Layouts help us reduce code duplication. They are html templates that be used on any page in your site and wrap around the content.
    ```bash
    mkdir _layouts
    touch _layouts/default.html
    ```

9. Add the following code to `_layouts/default.html`. The `{{ }}` and `{% %}` are [Liquid](https://jekyllrb.com/docs/liquid/) syntax for objects and tags respectively. Objects contain content to be displayed on the page. Tags create logic and control flow for templates. Here we are getting the page title and content objects, as well as including the navigation.html file (we will create this file in the next steps).
    ```html
    <!doctype html>
    <html>
        <head>
            <meta charset="utf-8">
            <title>{{ page.title }}</title>
        </head>
        <body>
            {% include navigation.html %}
            <hr>
            {{ content }}
        </body>
    </html>

10. Create a `_includes` folder and create a file called `navigation.html`. Includes are snippets that can be used elsewhere. This also helps avoid duplication. Here we will use it for navigation and include it, else we would have to write a navigation menu on each page.
    ```bash
    mkdir _includes
    touch _includes/navigation.html
    ```

11. Add the following code to `_includes/navigation.html`.
    ```html
    <nav>
        {% for item in site.data.navigation %}
        <a href="{{ item.link }}">{{ item.name }}</a>
        {% endfor %}
    </nav>
    ```

12. In the code above we are referring to the object `site.data.navigation`. Jekyll supports loading of data from various file types such as YAML, JSON, and CSV. These files must be located in the data directory. Create the `_data` directory and a file called `navigation.yaml`. More information on Jekyll variables can be found [here](https://jekyllrb.com/docs/variables/).
    ```bash
    mkdir _data 
    touch _data/navigation.yaml
    ```

13. In `_data/navigation.yaml` add the following code. We will add additional links as we add pages to our site.
    ```yaml
    - name: Home
      link: /
    ```

14. In the `index.md` front matter add `layout: default`. Your `index.md` file should now look like this.
    ```markdown
    ---
    layout: default
    ---
    # My Website
    Welcome to my website!
    ```

15. You should see `Home` at the top of your website. If not, you may need to rerun the jekyll build and serve command.
    ```bash
    bundle exec jekyll serve
    ```

16. Let's add an `About Me`, `Publications` and `Post` page. Create `about.md`, `publications.md`, and `posts.md` in the root directory, as well as a folder called `_posts`.
    ```bash
    touch about.md
    touch publications.md
    touch posts.md
    mkdir _posts
    ```

17. Add the following code to `about.md`.
    ```markdown
    ---
    layout: default
    ---
    # About Me
    ```

18. Add the following code to `publications.md`.
    ```markdown
    ---
    layout: default
    ---
    # Publications
    ```

19. Add the following code to `posts.md`.
    ```markdown
    ---
    layout: default
    ---
    # Posts
    ```

20. Add each page to the navigation bar by adding the appropriate details into `_data/navigation.yaml`. Your `_data/navigation.yaml` shoud look something like this.
    ```yaml
    - name: Home
      link: /
    - name: About Me
      link: /about
    - name: Publications
      link: /publications
    - name: Posts
      link: /posts
    ```

21. Jekyll was originally created for blogging. Jekyll blog posts go in a folder called `_posts`. However, they must follow the format `yyy-mm-dd-title.md`. Create two posts in the `_posts` directory.
    ```bash
    touch _posts/2022-10-29-post_1.md
    touch _posts/2022-10-29-post_2.md
    ```

22. Add the following code to each post. Changing `<number>` to 1 or 2 respectively.
    ```markdown
    ---
    title: Page <number>
    ---
    The first paragraph will be the excerpt for post <number>.

    This second paragraph has some more information.
    ```

23. You will notice we didn't include the `layout` key and value in the front matter. We are going to create a new layout for our posts. We want them to look slightly different to other pages. Create the file `_layouts/post.html` and add the following code. Here we are inheriting from the default layout.
    ```html
    ---
    layout: default
    ---
    <h1>{{ page.title }}</h1>

    <p>
    {{ page.date | date_to_string }}
    {% if page.author %}
        - Written by {{ page.author }}
    {% endif %}
    </p>

    {{ content }}
    ```

24. Instead of adding `layout: post` to each of our posts. We can create `_config.yml` and set configuration defaults, including the default layout type for certain folders or page types. Creata a `_config.yml` file in the root directory and add the following code. Information on front matter defaults can be found [here](https://jekyllrb.com/docs/configuration/front-matter-defaults/). The second set here is setting the default for all files of type `pages` to use the default layout. If you wanted you could go remove the `layout` key from our existing files `index.md`, `posts.md`, `publications.md`, and `about.md`. You will need to re-serve the website locally any time you change the configuration file.
    ```yaml
    defaults:
        - scope:
            path: ""
            type: "posts"
          values:
            layout: "post"
            author: "Jane Bloggs"
        - scope:
            path: ""
            type: "pages"
          values:
            layout: "default"
    ```

25. You will most likely deploy your website using GitHub pages. GitHub page servers are probably not located in your time zone. This can lead to some odd things sometimes happening. To help with this add the following `timezone: Australia/Brisbane` to the `_config.yml` file.

26. Change `posts.md` to the following to create a list of your posts.
    ```markdown
    ---
    ---
    # My Posts

    {% for post in site.posts %}
    ## [{{ post.title }}]({{ post.url }})
    {{ post.excerpt }}
    {% endfor %}
    ```

27. Okay that is enough about blogging. Posts are effectively a special type of what are called *Collections*. We can use [collections](https://jekyllrb.com/docs/step-by-step/09-collections/) to our advantage when things belong to the same group, publications for example. To create a collection make a folder using the format `_<collection_name>` and add this collection name to our configuration file under the `collections` key. In this example, create a folder called `_publications` and then add the following to `_config.yml`.
    ```yaml
    collections:
        publications:
    ```

28. In the `_publications` folder create two files `_publications/pub1.md` and `_publications/pub2.md`. You will notice we don't have to follow a special naming format like posts.

29. Copy the following into `_publications/pub1.md`. 
    ```yaml
    ---
    title: 2D Visual Place Recognition for Domestic Service Robots at Night
    paper_url: https://arxiv.org/pdf/1605.07708.pdf
    ---
    Paper abstract could go here.
    ```

30. Copy the following into `_publications/pub2.md`. 
    ```yaml
    ---
    title: Unsupervised Selection of Optimal Operating Parameters for Visual Place Recognition Algorithms using Gaussian Mixture Models
    paper_url: https://eprints.qut.edu.au/206690/1/73000457.pdf
    ---
    Paper abstract could go here.
    ```

31. Change `publications.md` to the following to create a list of your publications.
    ```markdown
    ---
    ---
    # My Publications

    {% for publication in site.publications %}
    ## [{{ publication.title }}]({{ publication.paper_url }})
    {{ publication.excerpt }}
    {% endfor %}
    ```

32. Re-serve the website locally, since we made changes to `_config.yml`, to view the results.

33. The website is fairly bland. There are no pictures, CSS, or JavaScript. Images, CSS, and JavaScript are known as assets and are placed in an assets folder. We will make this folder in the next step. First, we will create a [Sass](https://sass-lang.com/) file. Sass is an extension to CSS and is baked into Jekyll. Create a `_sass` folder in the root directory and add a `main.scss` file with the following code. This code will make the font size of each navigation bar item larger and all `<h1>` headings to be coloured green.
    ```css
    h1 {
        color: green;
    }

    .navbar-item {
        font-size: 1em;
    }
    ```


34. Create the `assets` folder in the root directory, then create the `css` folder within the `assets` directory. Copy the following code into a Sass file located at `assets/css/styles.scss`. The front matter blocks tells Jekyll that it needs to process this file remember, that is why they are included here. This code tells Sass to look for a file called `main.scss` located in a `_sass` directory within the root folder.
    ```sass
    ---
    ---
    @import "main";
    ```

35. Now reference this stylesheet in our default layout. Remember the posts layout inherits from the default, so we only need to add the stylesheet in one location. Add `<link rel="stylesheet" href="/assets/css/styles.css">` between the `<head>` and `</head>` tags in `_layouts/default.html`. The `_layouts/default.html` should now look like this.
    ```html
    <!doctype html>
    <html>
        <head>
            <meta charset="utf-8">
            <title>{{ page.title }}</title>
            <link rel="stylesheet" href="/assets/css/styles.css">
        </head>
        <body>
            {% include navigation.html %}
            <hr>
            {{ content }}
        </body>
    </html>
    ```

36. Update `_includes/navigation.html` to use the new CSS class. Change `_includes/navigation.html` to the following code.
    ```html
    <nav>
        {% for item in site.data.navigation %}
        <a href="{{ item.link }}" class="navbar-item">{{ item.name }}</a>
        {% endfor %}
    </nav>
    ```


**Congratulations!** You now have a very basic website where content can be easily added. Want to write anothe blog post or add your most recent publication, simply add another markdown file.

## Deploying using GitHub Pages

While a local website might be useful sometimes, we ideally want to publish our website to the world. We can do this using GitHub pages. However, before we get started there are some things to note.

- Each github user and organisation gets one user/organisation webpage and can have unlimited number of project webpages. The name of your repo will dictate if it is your user webpage or a project page, as well as the URL. For your user webpage, create a repo with the name following the format `<github_username>.github.io`. For project webpages, you the repo can be called whatever you would like. User webpages will have the URL `<github_username>.github.io`. Project webpages will have URLs of the form `<github_username>.github.io/<repo_name>`.

1. Create an empty GitHub repository.

2. Initialise the local version of your repository using the root directory of your website. Push the files to the remote repository.

3. Once pushed, on the repository's GitHub page, go to `Settings > Pages`. Under *Build and Deployment*
    - Set *Source* to: `Deploy from a branch`
    - Set *Branch* to: `main`
    - Leave the directory as `/(root)`
    - Click save.

4. If your repo is using your user webpage, it will now be live at `<github_username>.github.io` (or after a minute or so). If your repo is a project webpage, it will be live at `<github_username>.github.io/<repo_name>`. 

GitHub is running a GitHub Action under the hood. If your website is not live in a few minutes check the actions tab in your repository. This will be your first port of call for debugging deployment.

### Project Webpages - Configuring BaseURL
If you are using a project webpage you might find the links on the live site are not working. You might be getting a 404 error or a redirection to a page you weren't expecting to see. This is because we need to set the `baseurl` configuration variable correctly. Some initial information detailing `baseurl` can be found [here](https://jekyllrb.com/docs/upgrading/0-to-2/#baseurl)

1. In `_config.yml` add the line: `baseurl: /<repo_name>`
2. In `_layouts/default.html` change the stylesheet link to the following: `<link rel="stylesheet" href="{{site.baseurl}}/assets/css/styles.css">`
3. In `_includes/navigation.html` change the `href` line to the following: `<a href="{{site.baseurl}}{{ item.link }}" class="navbar-item">{{ item.name }}</a>`
4. In `posts.md` change `## [{{ post.title }}]({{ post.url }})` to `## [{{ post.title }}]({{ site.baseurl }}{{ post.url }})`.
5. Test the website locally by running `bundle exec jekyll serve`, it will be served at `localhost:4000/<repo_name>`, and make sure everything looks good (links and styles).
6. Push the changes to GitHub and check the live page.

## A Little More About Gemfile and Bundler

A Gemfile is a list of dependencies required by your Ruby project. Bundler is a special Gem and acts like a package manager and virtual environment. If you are familiar with Python and Pip, you can think of a Gemfile similar to a requirements.txt and Bundler similar to Pip. However, we can also use bundler to execute commands using the current environment by prefixing the command with `bundle exec`. If you wish, do the following.

1. While in your website root directory, run `jekyll -v`. You should get `Jekyll 3.9.x` printed to the terminal.
2. Now, also in your website root directory, run `bunbdle exec jekyll -v`. You should again get `Jekyll 3.9.x` printed to the terminal.
3. Go up on level (i.e., to the directory containing your website root folder), and run `jekyll -v`. You most likely will get `Jekyll 4.3.x`.

In steps 1 and 2 we are calling the project specific Jekyll Gem, as indiciated by our Gemfile. In steps 3 we are calling the system wide installed Jekyll Gem. Why can't we simply do step 1 then? Well, while it is calling the project specific Jekyll Gem, it may end up still using system wide installed Gems. Hence, if we do the second option we are saying to use the Gems as specified by the Gemfile. 

Hold up? We never specified a Jekyll version in our Gemfile. We only had two lines of code.

```ruby
source "https://rubygems.org"
gem "github-pages", "~> 220", group: :jekyll_plugins
```

The [github-pages](https://github.com/github/pages-gem) Gem is a gem that install the Gem versions utilised by the GitHub Pages servers. This includes a dependency on Jekyll 3.9.x, currently. Hence, why we are getting access to Jekyll. You can see a full list of github-pages Gem dependencies [here](https://pages.github.com/versions/). Using this Gem is the easiest way if:

- You want to use GitHub Pages in-built GitHub Action for building and deploying your website; and
- You want to guarantee that what you get on your locally served version is the same as what GitHub Pages will produce.

You may have noted that Jekyll is up to version 4+ but, GitHub Pages only using `3.9.x` currently. So, while using github-pages Gem and the in-built GitHub Pages Action for deploying is nice and easy, it doesn't give us the latest and greatest. 

## Getting the Latest and Greatest - Using Custom GitHub Actions

Originally when GitHub Pages was released in 2008, the build and deploy process was quite obscure and hard to debug when things went wrong. However, the release of [GitHub Actions](https://github.com/features/actions) in 2018 allowed for automatic build and deployment pipelines. GitHub Pages started using GitHub Actions in December 2021 for all public repositories. So, while it is relatively new, it is actually quite simple to create a custom action.

We will first start by recreating the in-built action used to deploy GitHub Pages.

1. First create the path `.github/workflows` within the website root directory.
2. Go to the [pages starter-workerflows](https://github.com/actions/starter-workflows/blob/main/pages) provided by GitHub Actions (this is a great place to look for starting points for other actions too).
3. Copy the contents of the `jekyll-gh-pages.yml` to a file called `jekyll-gh-pages.yml` located at `.github/workflows`. Tip: click on raw to make it easier to copy.
4. In your copy of the workflow change `[$default-branch]` to `main`. This tells it to run the workflow on pushes to main. 
5. In your repository online, go to Settings > Pages and change the Source to GitHub Actions.
6. Push your changes to the remote repository. If you go to the Actions tab on the repository you should see our new Action running, and after a minute or so, your site will be live. 

There done! We have recreated the effect of the in-built GitHub Pages action and we have complete control over the automatic build process. What if I want to use the latest and greatest? Easy, in the [pages starter-workerflows](https://github.com/actions/starter-workflows/blob/main/pages) you should see a starter called `jekyll.yml`. Let's try and use that now. However, first, let's disable our current workflow. 

1. Go to the Actions tab in your online repo. 
2. Click the `Deploy Jekyll with GitHub Pages dependencies preinstalled` action on the left. 
3. Click on the three dots next to the `Filter workflow runs` search bar.
4. Click `Disable workflow`.

Now, let's create a workflow to use the latest and greatest Jekyll. 

1. First, we should change our Gemfile to use the Jekyll Gem rather than the Jekyll Gem that comes with the github-pages Gem. Do this by removing or commenting out the line `gem "github-pages", "~> 220", group: :jekyll_plugins` and by adding `gem "jekyll", "~> 4.3.1"`. If you want to know what the `"~> 4.3.1"` is doing, you can get some information [here](https://thoughtbot.com/blog/rubys-pessimistic-operator).
2. Run `bundle update` in the terminal and re-serve the local website. Running `bundle update` will update the Gems based on what is on the Gemfile. You can check this by then running `bundle exec jekyll -v`, you should get `jekyll 4.3.x`.
3. Copy the contents of the `jekyll.yml` [starter workflow](https://github.com/actions/starter-workflows/blob/main/pages) to a file called `jekyll.yml` located at `.github/workflows`. Hint: the filename you use doesn't have to match what GitHub uses.
4. In your copy of the workflow change `[$default-branch]` to `main`.
5. Push your changes to the remote repository and watch your new action do its thing!

There you have it, we are now using the latest and greatest!
