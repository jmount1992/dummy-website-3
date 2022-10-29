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

3. Add the following to the Gemfile.
    ```ruby
    source "https://rubygems.org"
    gem "jekyll"
    ```

4. Install the Gems
    ```bash
    bundle install
    ```

5. Create an `index.md` to act as the homepage.
    ```bash
    touch index.md
    ```

6. Add the following codee to `index.md`. Any code between the two sets of `---` are what is called front matter. Jekyll will only process any files that contain front matter. That is why this ReadMe file gets skipped. It does not contain any front matter.
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

9. Add the following code to `_layouts/default.html`. The {% raw %}`{{ }}` and `{% %}`{% endraw %} are [Liquid](https://jekyllrb.com/docs/liquid/) syntax for objects and tags respectively. Objects contain content to be displayed on the page. Tags create logic and control flow for templates. Here we are getting the page title and content objects, as well as including the navigation.html file (we will create this file in the next steps).
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

### Project Webpages - Fixing Broken Links
If you are using a project webpage you might find the links on the live site are not working. You are getting a 404 error or a redirection to a page you weren't expecting to see. This is because we need to set the `baseurl` configuration variable correctly. Some initial information detailing `baseurl` can be found [here](https://jekyllrb.com/docs/upgrading/0-to-2/#baseurl)

1. In `_config.yml` add the line: `baseurl: /<repo_name>`
2. In `_layouts/default.html` change the stylesheet link to the following: `<link rel="stylesheet" href="{{site.baseurl}}/assets/css/styles.css">`
3. In `_includes/navigation.html` change the `href` line to the following: `<a href="{{site.baseurl}}{{ item.link }}" class="navbar-item">{{ item.name }}</a>`
4. Test the website locally by running `bundle exec jekyll serve`, it will be served at `localhost:4000/<repo_name>`, and make sure everything looks good (links and styles).
5. Push the changes to GitHub and check the live page.