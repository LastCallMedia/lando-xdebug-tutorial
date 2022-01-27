
# Lando XDebug Tutorial

Running a debugger when developing in PHP is often a skill that developers put off learning for a surprisingly long amount of time. Additionally, with the number of development setups available and the intricacies of setting up XDebug, it can often be challenging to get started. This tutorial is written to help developers get started with XDebug using [Lando](https://lando.dev/) and [Visual Studio Code](https://code.visualstudio.com/)

## Initial setup

You should already have [Lando](https://lando.dev/) and VS Code installed on your computer. To get started clone the repo, open a terminal and change into the repo's root directory. Run `./init.sh` to download the latest version of WordPress and start Lando.

## Set up WordPress

Load up your new WordPress site at https://my-first-wordpress-app.lndo.site/. Run through the installation steps shown in the browser. The only values you'll need to get right are the database settings which by default are the values Lando uses:

```
Database Name: wordpress
Username: wordpress
Password: wordpress
Database Host: database
Table Prefix: wp_
```

## Enable debugging

Next load up the project in VS Code, switch to the debugger tab and turn it on. Specifically you'll want to navigate to the "Run and Debug" VS Code tab and click the green, play-button-shaped icon next to "Listen for XDebug":

![Turn on Xdebug](/images/turn_on_xdebug.png)

## Set a breakpoint

Now that the site is running and XDebug is listening for errors, let's set a breakpoint to see the debugger working. Open `wordpress/wp-includes/class-wp-query.php` and find the `the_post()` function (in version 5.9 this is on line number 3391). A couple lines below the start of the function, click next to the line number (3393 in version 5.9). A dot should appear indicating that you've set a breakpoint. Load the homepage of your WordPress site and VS Code should indicate that the breakpoint was hit:

![The breakpoint is hit and VS Code shows a list of variables which you can inspect](/images/normal_breakpoint.png)

A couple things to try:

1. Check the variables list. Browse `$post` and `$this`. Oftentimes the variables in WordPress are quite complex to understand. Developers frequently use functions like `print_r()` to see what the variables are set to. Using XDebug provides an easier way to see these values.
2. In the top right XDebug gives a set of options you can use to navigate the code. Particularly useful are the step over and step into buttons. Play with these to understand how you can go through the flow of the code.

Once you're done playing, click the "Play" button, which is the left-most button of the debugger controls. This button allows the server to continue rendering the website.

## Catch an exception

Another useful feature of XDebug is that it lets you automatically break on exceptions.

To see this, ensure exception breakpoints are turned on. Visit the "Run and Debug" pane and ensure "Exceptions" is checked in the "BREAKPOINTS" sub pane:

![Exceptions turned on](/images/exception_setting.png)

Next, add the following line below the line where you set the breakpoint in the previous section:

```php
throw new Exception('uhoh');
```

Be sure to uncheck any breakpoints you previously checked.

Load the homepage of your site and you'll see it automatically breaks on the Exception:

![Breakpoint triggered by an exception](/images/exception_breakpoint.png)

This feature allows you to automatically be shown the details of any exception in a way that helps you debug it. Often, the most useful thing to look at when you have an exception is the "CALL STACK" sub pane of the "Run and Debug" pane. This sub pane shows you all the functions the code executed on the way to the current exception. You can use it to understand the path of the problem.

## Final thoughts

You're up and running now. Exploring some more might be a good idea. Also check out the following files in this repo:

* [launch.json](.vscode/launch.json) - This file is how we configure VS Code to connect to Lando's XDebug.
* [php.ini](config/php.ini) - This file configures PHP in a way that makes it easy to run the debugger. If you change this file, you'll likely need to run `lando rebuild` to see the results.
* [.lando.yml](.lando.yml) - This file configures Lando. Note that XDebug needs to be specifically turned on here, and this file also needs to be told where to find the [php.ini](config/php.ini) file.

Those files are really all it takes to get up and running. You can bring them over to your projects to set them up. Remember, you'll likely need to run `lando rebuild`.
