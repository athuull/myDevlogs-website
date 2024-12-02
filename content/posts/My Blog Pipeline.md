---
title: "My Blog Pipeline"
date: 2024-12-01
draft: false
tags: ["tools", "blogging", "workflow"]
---

Here's how I manage and run this blog:

1. **[Obsidian](https://obsidian.md)**  
   Obsidian is my go-to note-taking and writing tool. I use it to draft, organize, and refine all my posts before publishing. Its markdown-based workflow is perfect for my use case such as this website.

2. **[Hugo](https://gohugo.io)**  
   Hugo is the static site generator powering this blog. It allows me to convert markdown files into a fast and beautifully designed website with ease.

3. **[GitHub](https://github.com)**  
   GitHub serves as my version control and deployment tool. I push my Hugo-generated website to a GitHub repository, which keeps my code and content organized and backed up.

4. **Private Server on Oracle Cloud**  
   I host my website on a free private server provisioned through [Oracle Cloud](https://www.oracle.com/cloud/). I like hosting my own websites. I am also hosting this website on Github pages.

With this setup, I can write in Obsidian, use Hugo to generate the site, manage my source code with GitHub, and host it securely on my Oracle Cloud server. This pipeline ensures a smooth and efficient workflow for maintaining this blog.

# Setting up Hugo

## 1. Installation

### On Windows:
1. Download the Hugo executable from the [official website](https://gohugo.io/getting-started/installing/).
2. Add the Hugo binary to your system's PATH variable, so you can run it from the command line.
3. Verify the installation by running `hugo version` in your terminal.

### On Mac:
1. Use Homebrew to install Hugo by running the following command:
   ```bash
   brew install hugo
   ```

### On Linux: 
1. Install using your package manager: 
   ```bash
   sudo apt-get install hugo
   ```
2. Verify installation by running `hugo version`

## 2. Creating a Hugo Website

After Hugo is installed, create a new website using the command:
```bash
hugo new site website_name
```
This creates a new Hugo site in the `website_name` folder.

## 3. Downloading Hugo Themes
Browse [official Hugo themes site](https://themes.gohugo.io/) for customizing the look of the website. To install a theme: 

1. Navigate to the website folder:
   ```bash
   cd website_name
   ```
2. Download your desired theme into the `themes` directory. In my case, I used:
   ```bash
   git init
   git submodule add https://github.com/LordMathis/hugo-theme-nightfall themes/nightfall 
   ```

## 4. Setting up `hugo.toml`

`hugo.toml` is the configuration file for your hugo website. It is where you need to set the global parameters such as title, language, theme etc. You can find more about this in the theme's documentation.

## 5. Creating the blog folder inside `content`
Hugo uses a folder structure to organize the website content. You need to create a folder to hold the markdown file that you need to convert into a website page.

```bash
cd content
mkdir blog
```

## 6. Copy markdown files from Obsidian folder to `content/blog`

You will need to copy the markdown files from the Obsidian folder to the `content/blog` folder so Hugo can convert it into webpages.

Run the commands replacing the directories with the source directory and destination directory respectively:

### On Windows : 
```bash 
robocopy \path\to\obsidian\folder \path\to\content\blog /E
```

### On Linux  :
```bash
rsync -av --progress /path/to/obsidian/ /path/to/website_name/content/blog/
```

**Note:** 
You can find the directory of the obsidian folder by right clicking on the folder inside Obsidian and clicking "Show in folder".

## 7. Building the website
Once you have your markdown content ready and copied over to the `content/blog` folder:

1. Build website using:
   ```bash
   hugo
   ```

2. Start the development server in your browser using: 
   ```bash
   hugo server
   ```
   Your website will be available at `http://localhost:1313/`.

## 8. Publishing the website

You can use Github pages or deploy to your own server or even a hosting provider.

Using Github Pages: 
1. Push Hugo generated static files (found in public/) directory to your Github repo.
2. Set up Github pages to serve content from the public directory.

For a private server:
1. Transfer the `public` folder contents to your server.
2. Serve the files using a web server like Nginx or Apache.

**Note:** For a more efficient workflow, automate `rsync/robocopy`, `hugo`, `hugo server` using a powershell/bash script.