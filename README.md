# Eleventy-From-Scratch Course Windows Node Set-Up & Fixes
A Node JS package set-up for the 'Eleventy from Scratch' course for Windows development environments, plus a critical source code change.

## TL;DR
Use the package.json to install the required node modules and npm scripts.

Edit \gulp-tasks\sass.js line ```const sourceFileName = /[^/]*$/.exec(history[0])[0];``` to be ```const sourceFileName = /[^\\]*$/.exec(history[0])[0];```

Create an empty src/_includes/css/page.css

## Issues Encountered

While working through the eleventy static site generatorcourse https://piccalil.li/course/learn-eleventy-from-scratch/ I hit some issues primarily stemming from using Windows as a development platform:

1. A regex to extract a file name failed.
2. A file reference was missing (not Windows related).
3. 'Concurrently' Node JS module did not work.
4. Node script 'production' did not work.

## Lesson 19: Setting up Sass
This is where I got the regex and Concurrently issues. At the end of the lesson, running the updated start script returned the following error:

```
> npx gulp && concurrently 'npx gulp watch' 'npx eleventy --serve'

[20:17:17] Using gulpfile ~\OneDrive\Web Development\11ty\piccalilli\zzz\gulpfile.js
[20:17:17] Starting 'default'...
[20:17:17] Starting 'sass'...
[20:17:17] Finished 'sass' after 172 ms
[20:17:17] Finished 'default' after 180 ms
[0] ''npx' is not recognized as an internal or external command,
[0] operable program or batch file.
[2] 'watch'' is not recognized as an internal or external command,
[2] operable program or batch file.
[3] ''npx' is not recognized as an internal or external command,
[3] operable program or batch file.
[2] watch' exited with code 1
[0] 'npx exited with code 1
[3] 'npx exited with code 1
[4] (node:39460) ExperimentalWarning: The fs.promises API is experimental
[4] Caching: https://11ty-from-scratch-content-feeds.piccalil.li/media.json
[1] [20:17:20] Using gulpfile ~\OneDrive\Web Development\11ty\piccalilli\zzz\gulpfile.js
[1] [20:17:20] Starting 'default'...
[1] [20:17:20] Starting 'sass'...
[4] Writing dist/feed.xml from ./src/rss.html.
[4] Writing dist/people/1/index.html from ./src/people/1.md.
[4] Writing dist/people/2/index.html from ./src/people/2.md.
[4] Writing dist/people/3/index.html from ./src/people/3.md.
[4] Writing dist/people/4/index.html from ./src/people/4.md.
[4] Writing dist/people/5/index.html from ./src/people/5.md.
[4] Writing dist/people/6/index.html from ./src/people/6.md.
[4] Error writing templates: (more in DEBUG output)
[4]     Template render error: (./src/_includes/layouts/about.html)
[4]       Error: template not found: css/critical.css
```

There's something going on with the updated start script resulting in ` ''npx' is not recognized as an internal or external command,` etc. And at the end you can see `Error: template not found: css/critical.css`.

I wasn't able to resolve the script issue. Just running `eleventy --serve` I still got the "template not found error". This turned out to be a regex issue in \gulp-tasks\sass.js. The original code, a regex to extract the filename from a full path,  will not work on Windows as it uses backslashes:

__original code:__ ```const sourceFileName = /[^/]*$/.exec(history[0])[0];```

the fix was to replace the forward slash with an escaped backslash:

__fixed code:__ ```const sourceFileName = /[^\\]*$/.exec(history[0])[0];```

Thereafter, I got to the next issue with the following error:

```
TemplateWriterWriteError was thrown
> (./src/_includes/layouts/feed.html)
  Error: template not found: css/page.css
  ```
 The file has just not been created yet in the course (it's done in a future lesson). I solved that by creating an empty src/_includes/css/page.css.
 
 With the course issues resolved, I looked into 'concurrenty' but could not get it to run. Eventually I installed 'npm-run-all' and used the following npm scripts:
 
```
    "gulp": "npx gulp",
    "gulp:watch": "npx gulp watch",
    "eleventy": "npx eleventy --serve",
    "start": "npm-run-all gulp --parallel gulp:watch eleventy"
```

## Lesson 31: Wrapping Up
The last issue I ran into was in the final lesson, setting up the npm production script. The original script is:
```
"production": "NODE_ENV=production npx gulp && NODE_ENV=production npx eleventy"
```
The issue I hit here was that the production environment was not invoked. This was resolved by installing the 'cross-env' Node JS module and modifying the script as follows:
```
"production": "cross-env NODE_ENV=production npx gulp && cross-env NODE_ENV=production npx eleventy"
```

Use the package.json to install the required node modules & scripts. Note, all modules for the complete course are included.
