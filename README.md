# tiktok-to-ytdlp

Fetch all the liked videos, videos from an user, videos with a specific sound
etc. from TikTok, and creates a script to download them with yt-dlp

## Instructions

### From the extension

Download the extension by following the instructions you can find in
[the extension README](./extension/README.md). Then, change the conversion
options (if you want to), and click on the button to start the conversion. The
extesion will take care of everything else.


### Scraping TikTok Video URLs & Metadata

Extract video details (URLs, views, desc, dates, hashtags.) and save them in a structured JSON file.

**Example**

```
[
  {
    "views": "248700",
    "desc": "C++, C#, php, javascript, lua, python, rust edit in 1 video ",
    "hashtags": "#edit,#programminglanguage,#fyp,#software,#programming,#softwareengineer,#csharp,#csharpprogramming,#cplusplus,#php,#phpcoding,#javacsript,#lua,#python,#pythonprogramming,#rust,#softwaredevelopment,#software,#programmingedit,#coding,#codingedit,#pythonprogramminglanguage,#cplusplusprogramming",
    "publishDate": "4-3",
    "scrapedAt": "2025-05-10T08:14:12.562Z",
    "caption": "",
    "url": "https://www.tiktok.com/@maxae.exe/video/7489284783457996048"
  }
]
```

### Comments 

Extract comments from a specific TikTok video and save them in JSON format.


**Example**

```
[
  {
    "comment": "programming edit? thats new",
    "likes": "467",
    "publishDate": "4-5",
    "scrapedAt": "2025-05-10T08:14:38.473Z",
    "url": "https://www.tiktok.com/@gigin425"
  }
]
```

### From the console

Open the TikTok webpage of the user/sound/etc. you want all the videos
downloaded. Press Ctrl (or Cmd if you are on a Mac) + Shift + I to open the
Developer Tools. Go into the Console tab on the top (if you don't see it, click
on the `>>`) and paste the content you can find in the
[script.js](https://raw.githubusercontent.com/Dinoosauro/tiktok-to-ytdlp/main/script.js)
file. If you prefer a minified version, you can find that
[here](https://raw.githubusercontent.com/Dinoosauro/tiktok-to-ytdlp/main/script.min.js).
Press enter.

### Next steps

The webpage will automatically scroll until no other items are found. Then, a
file called "TikTokLinks.txt" will be downloaded. You now can download the
videos with yt-dlp. An example script is:

`yt-dlp -a TikTokLinks.txt -o "TikTok/%(uploader)s/%(title)s - %(id)s.%(ext)s"`

Note that the most important part in this script is `-a TikTokLinks.txt`: by
writing this, yt-dlp will download all the videos that are in the txt file. You
can then add all the arguments you prefer to yt-dlp

### Ask for intermediate files

If you need to download a really long list of TikTok, you may want to start
downloading them while the page continues scrolling. To do that, you can
download an intermediate file, that will contain all of the links up to the
point the page has scrolled. To do that, write in the console `requestTxtNow()`,
press enter and a `TikTokLinks.txt` file will be downloaded. If you're using the
extension, you can find a "Get partial file" button. Click it and the file will
be automatically downloaded.

By default, the links in the first intermediate files will be deleted from the
final (or the second/third etc. file if you want to download more intermediate
files) file, so that you won't download any duplicates. You can change that by
putting the `delete_from_next_txt` option to false.

## Script options

You can edit the values of the first five lines of the script to change some
useful settings:

- `scrolling_min_time` & `scrolling_max_time`: change the thread sleeps between
  a scroll and the next one.
- `min_views`: don't add a video to the text file if it has fewer than _x_
  views.
- `delete_from_next_txt`: put this to false if you want that the final txt files
  has all the links, even the ones you've already downloaded with intermediate
  files.
- `output_name_type`: choose the format for the output file name. Valid inputs
  are:
  - A generic string: the file name will be exactly that
  - 0 (as integer): try fetching the title from some data tags
  - 1 (as integer): the webpage title will be used
  - 2 (as integer; default): the first title on the page (h1 HTML elmenet) will
    be used
  - Any other value: "TikTokLinks.txt" will be used as a file name
- `adapt_text_output`: replace Windows unsafe characters for the output file
  name.
- `allow_images`: save also TikTok photos (if disabled, only videos will be
  fetched).
- `export_format`: you can choose if you want to save the result as a TXT file
  (`txt`) or as a JSON file (`json`)
- `exclude_from_json`: populate this array with the keys you don't want to save
  in your JSON file. By default, the JSON file has the `url`, `caption` and
  `views` keys.

### Advanced script options

These really useful options permit to change how the script fetches data, and
what it should do when errors are found.

- `get_array_after_scroll`: change this if you want to fetch all the TikTok
  videos when the script has finished scrolling, and not after every single
  scroll. Keep in mind that it seems that TikTok doesn't unappend the previous
  videos, so this option shouldn't be relevant, but this might change in the
  future.
- `get_link_by_filter`: use the new method of getting all the links in a video
  container, and then look for the one that contains the video URL structure. If
  false, the "old" method of using data attributes will be used (even if I
  couldn't find more tags, I can't garantee that this old method has all of
  them).
- `check_nullish_link`: check if the link is nullish, and, since it would be
  useless to append it, continue with the next link. It's reccomended to leave
  this set to true.
- `log_link_error`: write to the console if a link is nullish.
- `maximum_downloads`: if the number of fetched items is above this number, the
  fetching process will be stopped
  - The `get_array_after_scroll` variable must be set to false
  - If more items than this number can be fetched without continuing scrolling,
    they'll also be added. Therefore, the output number might be greater than
    this variable.
- `delete_from_dom`: Delete the previous items form the DOM, so that
  performances can be improved. Note that this is experimental and might break
  TikTok's webpage in the future. Therefore, use it only if you need to (for big
  pages)
  - Remember to set the `get_array_after_scroll` variable to false. Otherwise,
    this value will be ignored.

## Warning:

This script is licensed under the MIT license.

Even if basically the only thing this script does is automatically scrolling the
webpage, so there's not that big of a risk, I don't claim any responsibilties at
all for the usage of this script and the eventual consequences.
