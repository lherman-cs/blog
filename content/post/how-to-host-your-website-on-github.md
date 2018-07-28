+++
categories = []
date = "2018-07-22T22:30:47-04:00"
image = "/uploads/photo-1426024120108-99cc76989c71.jpg"
tags = []
title = "How to Host Your Website on Github for Free Without Code!"
weight = 5
writer = "Lukas Herman"

+++
First of all, what is Github? According to Wikipedia, "Github is a web-based hosting service for version control using Git." This is great! You can store your code and its history too. But, its awesomeness doesn't end there. Rather than just storing the source code, Github also provides you **free website hosting.** You might think, "that totally makes sense." Since the source code is already there, it's very easy to simply host the website using the existing source code. Yup, you got that right!

_If you don't have a Github account yet, you can simply follow the registration procedure on Github's_ [_main page_](https://github.com/)
![github](https://i.imgur.com/JO7X36Y.png)

For the sake of this tutorial, I want to keep everything to be **super simple**. I'll have new posts in the future for more advanced topics. But, for now, we **won't use any git command**, but we'll **only use Github's web UI**.

So, without further ado, let's get started!

## 1. Create Your Website's Repository

After you log in, you should see something like the following:

![repo](https://blog.lherman.tk/content/images/2018/07/repo.png)

Now, we can just go ahead and click **"New repository"** button.

![create_repo](https://blog.lherman.tk/content/images/2018/07/create_repo.png)

Here, there are only **2 important fields** that you should worry about for now:

1. Your website's repository name
2. Your website's license

If you don't know which license to choose, you can refer to [this website](https://choosealicense.com/). Personally, I like **Apache License 2.0** because it's similar to **MIT License**, which gives some freedom for the community to use and modify, but it also protects me as the author of the project.

## 2. Create Your First HTML

By default, Github will look into your repository to get **index.html**. This file, essentially, is your website's gateway.

Let's create our first HTML file and name it **index.html**.

![create_button](https://blog.lherman.tk/content/images/2018/07/create_button.png)

![index_first](https://blog.lherman.tk/content/images/2018/07/index_first.png)
Make sure that you name the file as **index.html**.

![create_button](https://blog.lherman.tk/content/images/2018/07/create_button.png)
After that, you can simply click **Commit new file**.

If everything went well, you would something like the following:
![after](https://blog.lherman.tk/content/images/2018/07/after.png)

## 3. Publish Your Website

To publish your website, we need to go to our website's settings page:
![settings_button](https://blog.lherman.tk/content/images/2018/07/settings_button.png)

Then, scroll down until you see these settings and choose **master branch**:
![gh-pages](https://blog.lherman.tk/content/images/2018/07/gh-pages.png)

After you select **master branch**, you can now save it:
![save_button](https://blog.lherman.tk/content/images/2018/07/save_button.png)

## 4. Your Website is Published!

If you go back to the previous setting, you would see the following:
![published](https://blog.lherman.tk/content/images/2018/07/published.png)

Simply click on that link, you would see **"Hello world"** on your website!

![web](https://blog.lherman.tk/content/images/2018/07/web.png)

# Conclusion

In this tutorial, we've covered **how to use Github as your free website server with the web UI only**, This tutorial might be too simple and it's not what you're looking for. Don't worry! I'll cover the more advanced topics in the future. So, that's it for now! As always, have fun with coding!