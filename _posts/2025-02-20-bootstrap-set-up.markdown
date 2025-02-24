---
layout: post
title:  "Bootstrap Set-up"
date:   2025-02-20 09:05:35 -0500
categories: jekyll update
---
The following links helped me set up bootstrap:
 
 - [Using SASS Files from Ruby Gems with Jekyll] [sass-from-ruby-gems]
 - [How to Add Bootstrap 5 Sass to Jekyll][bootstrap-5-sass]

[bootstrap-5-sass]: https://medium.com/codex/how-to-add-bootstrap-5-sass-to-jekyll-e3b189f71552
[sass-from-ruby-gems]: https://medium.com/insights-into-a-coders-brain/using-sass-files-from-ruby-gems-with-jekyll-90c4e020d75c

### Could not find a JavaScript runtime
 After implementing per instructions ran into an error:

``` ruby
kristoffernord@penguin:~/projects/test-site$ bundle exec jekyll --watch

bundler: failed to load command: jekyll 
(/home/kristoffernord/projects/test-site/vendor/bundle/ruby/3.1.0/bin/jekyll)

/home/kristoffernord/projects/test-site
/vendor/bundle/ruby/3.1.0/gems/execjs-2.7.0/lib/execjs/runtimes.rb:58:in 
`autodetect': Could not find a JavaScript runtime. 
See https://github.com/rails/execjs for a list of available runtimes. 
(ExecJS::RuntimeUnavailable)
```

The error "Could not find a JavaScript runtime" means that Jekyll (or one of its dependencies) needs a JavaScript engine to execute some JavaScript code, and it can't find one on your system.  This is a common issue, especially on systems where a JavaScript runtime isn't installed by default.

### Set-up Node Package Manager (npm)
Install Node.js (Recommended): Node.js comes with npm (Node Package Manager), which is very useful for managing JavaScript dependencies.  Installing Node.js is the most common and generally recommended solution.

How to install Node.js: The easiest way is usually through your system's package manager. If you're on a Linux distribution, you can often use apt, yum, or dnf. You can also download installers from the official Node.js website (nodejs.org).

Download and install nvm:
``` bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
```

Some instructions in lieu of restarting the shell didn't work for me:
``` bash
\. "$HOME/.nvm/nvm.sh"
``` 
Instead I just closed and re-opened the terminal but I possibly could have run this terminal command:
``` bash
USE INSTEAD
source ~/.bashrc 
```

### Download and install Node.js:
nvm install 22

#### Verify the Node.js version:
``` bash
node -v # Should print "v22.14.0".
nvm current # Should print "v22.14.0".
```
#### Verify npm version:
``` bash
npm -v # Should print "10.9.2".
```

``` ruby
kristoffernord@penguin:~$ cd projects/test-site
kristoffernord@penguin:~/projects/test-site$ nvm install --lts
Installing latest LTS version.
v22.14.0 is already installed.
Now using node v22.14.0 (npm v10.9.2)
kristoffernord@penguin:~/projects/test-site$ node -v
v22.14.0
kristoffernord@penguin:~/projects/test-site$ npm -v
10.9.2
```
Create a .nvmrc file in the root of your Jekyll project.  This file tells nvm which Node.js version to use when you're in that directory.
``` bash
nvm install --lts
nvm alias default lts/*
echo "lts/*" > .nvmrc
```
Now, whenever you cd into your project directory, you can simply run:
```
nvm use
```
nvm will automatically switch to the correct Node.js version specified in .nvmrc

### Override Theme CSS
Initial set-up instructions called for the ```main.scss``` file to be created in ```assets/css```.
This resulted in 2 ```main.css``` files being generated. One in ```_site\access``` and an additional on in ```_site\access\css```

#### Solution
Locate the jekyll theme directory, in my case it is 'minima'. 

```
/home/kristoffernord/projects/test-site/vendor/bundle/ruby/3.1.0/gems/minima-2.5.1
```

According to the README.md file the correct way to override the them css is to create ```main.scss``` in the same directory that that file is located in the theme. In this case it is in the ```assets``` directory.

#### Why this approach is best:

- **Theme Structure:** Minima (and many other Jekyll themes) typically organize their assets (CSS, JavaScript, images) within the assets directory.  By placing your main.scss in the same assets directory as the theme's main.scss, you're adhering to the theme's intended structure.

- **Sass Compilation:** Jekyll's Sass compiler, when correctly configured (with sass_dir: assets in your _config.yml), will look in the assets directory for your main.scss file.  It will then compile this main.scss (including any @imported files) into a main.css file within the assets/css directory in your _site folder.

- **Override and Customization:** This structure allows you to effectively override the theme's styles.  Your main.scss will likely import the theme's main.scss as a starting point and then add your customizations.  This way, you don't have to modify the theme's files directly, making theme updates much easier.

#### Best Practices:

1. ```_config.yml```:  Ensure your ```_config.yml``` file has the correct ```sass_dir``` setting:

    **YAML**
    ```
    sass:
    sass_dir: assets # Because your main.scss is in the assets directory
    style: :compressed # or :expanded for development
    ```
2. ```main.scss``` Structure:  Your ```main.scss``` should typically look like this:

    **SCSS**
    ```
    ---
    ---  // Important: Remove the YAML front matter if present.
    @import "minima"; // Import the theme's main.scss (adjust path if needed)
    @import "variables"; // Import your custom variables (if you have them)
    @import "mixins"; // Import your custom mixins (if you have them)

    // Your custom styles and overrides go here
    body {
        font-size: 18px; // Example override
    }
    ```
    - **Import Theme's** ```main.scss```: The ```@import "minima";``` line (or similar, depending on the theme) is crucial. It imports the theme's base styles so you can build upon them. Adjust the path if the theme's ```main.scss``` is in a subdirectory of ```assets```.
    - **Custom Styles**: Place your custom styles and overrides after the ```@import``` statement. This ensures that your styles override the theme's styles.
    - **No YAML Front Matter**: Sass files (```.scss```) should not have YAML front matter (```---```). Remove it from your ```main.scss``` file.

3. **HTML Link**: Your HTML should link to the compiled ```main.css``` in the ```assets/css``` directory:

    **HTML**
    ```
    <link rel="stylesheet" href="{{ site.baseurl }}/assets/css/main.css">
    ```
4. **Clean and Rebuild**: Always run ```bundle exec jekyll clean``` followed by ```bundle exec jekyll build``` or `

    ```bundle exec jekyll serve --watch``` after making changes to your Sass files or configuration.

By following these practices, you'll have a clean and maintainable way to customize your Jekyll site's styles using Sass, while avoiding the duplicate CSS file issue.