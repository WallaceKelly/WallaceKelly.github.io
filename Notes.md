#Notes about Wallace Kelly's blog#

##Directories##

1. **published**: The files that are currently published to the web. This is the master branch of `https://github.com/WallaceKelly/WallaceKelly.github.io.git`. 
2. **fsblog-wek**: The generator for the blog, based on FsBlog. This is the `fsblog` branch of `https://github.com/WallaceKelly/WallaceKelly.github.io.git.` Use `CopyToBlog.cmd` to copy the output of this generator into the `published` folder to publish.
3. **fsblog**: My fork of the original FsBlog project. To get updates, merge from the GitHub source into this fork. Build. Then use `fsblog-wek\CopyFromFsBlog.cmd` to copy to my version of FsBlog.

##FsBlogCommands##

In **fsblog-wek**

* `fake.cmd preview`: Generate the blog (into the Ouput folder) and start a local webservice. *However, the links are not relative, so the preview does not work correctly.*


