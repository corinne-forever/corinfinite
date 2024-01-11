---
share: true
---

# MarkDownload browser plugin

Note that it doesn't include the Front-matter by default in the generated files. This feature is helpful since it includes the URL of the original page and the download date.

Using this plugins:
* https://addons.mozilla.org/en-US/firefox/addon/markdownload/

See also: 
- https://github.com/deathau/markdownload/issues/120#issuecomment-1134121245

## Desktop notes
Obsidian vault needs to be placed in Download folder so the plugin can access it. Alternatively you can run a periodic job to copy the files.

## Configuration
My desired settings are as follows:
- Check "Append front/back template to clipped text"
- (On Moble): Set Download Mode to "Content Link"
  - Otherwise the download won't work on mobile. 
- Change the Front matter template to the following:
```md
---
created: {date:YYYY-MM-DDTHH:mm:ss} (UTC {date:Z})
tags: []
source: {baseURI}
author: {byline}
---

# {pageTitle}
```

## Setting up MarkDownload on Mobile
Our workflow is
- Use MarkDownload in Firefox Nightly to save pages
- Tasker periodically copies the md files from the Download folder into your Obsidian vault
- The files will be synced the next time you open the Obsidian app
### Install MarkDownload as a Firefox Nightly plugin
Install Firefox Nightly from the Play Store

Follow [these instructions](https://blog.mozilla.org/addons/2020/09/29/expanded-extension-support-in-firefox-for-android-nightly/) to create an AMO collection containing MarkDownload and enable extensiom support in nightly. 

... > About Firefox Nightly > Tap the Firefox logo 5 times to enable debug mode

The collection I made: https://addons.mozilla.org/en-US/firefox/collections/12524503/test/
Collection ID (user ID): 12524503
Collection name: test

### Configuring MarkDownload on mobile
... > Add-ons > Add-Ons Manager > MarkDownload > Settings
See Configuration section above
### Usage
... > Add-ons > MarkDownload > Download

### Tasker Task
Note that it's very easy to mess this step up. Always check, otherwise you could ve sending your downloaded file to a file named "saved_articles" instead of moving it to that folder. 

    Task: Move Markdown Files
    
    A1: List Files [
         Directory: Download
         Match: *.md
         Sort Select: Alphabetic
         Variable Array: %work
         Use Global Namespace: On ]
    
    A2: For [
         Variable: %work
         Items: %work()
         Structure Output (JSON, etc): On ]
    
        A3: Move [
             From: %work
             To: Documents/Brain/3Resources/saved_articles/
             Use Global Namespace: On ]
    
    A4: End For
    
    

# Other
Other ones to keep an eye on:
https://jplattel.github.io/obsidian-clipper/

## Bookmarklet
Bookmarklet: https://gist.github.com/kepano/90c05f162c37cf730abb8ff027987ca3
* This is okay, doesn't work on all sites

Site to generate JS: https://obsidian-clipper-maker.ganesshkumar.com/

# Web hosting
## Github Publish
For free to 4 dollars a month I can host this on Github with the [Github Publish](https://github.com/ObsidianPublisher/obsidian-github-publisher/tree/master) extension. The theme isn't as nice but I also don't love the default.

The behavior of the commands is a bit wonky. Doing a `Refresh all published notes` after making edits and there are no changes to upload. But do a `Refresh published and upload new notes` and it will generate a commit for every file on a branch  'VAULT_NAME-DD-MM-YYYY'. The diff is tiny, but it does seem weird that it generates a commit for each file. Maybe that's expected, but it is weird.

It does seem to be possible to get things into a weird state where changes end up on the 'VAULT_NAME-DD-MM-YYYY' branch and not merged in. **You should probably delete the branch after every upload.** This is done automatically if you 'Automatically merge pull requests' set in the plugin options, but otherwise it won't which I suspect caused issues due to the branch getting reused.
## Obsidian Publish
Obsidian Publish is 8/month which isn't a lot but I don't particularly love the default layout and long term MkDocs may be more flexible.
