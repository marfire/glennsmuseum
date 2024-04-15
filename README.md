# Installation
- [Download](https://www.python.org/downloads/) and install the latest Windows version of Python.
  - Make sure that it gets installed globally and added to the `PATH`.
- [Download](https://imagemagick.org/script/download.php#windows) and install the latest Windows version of ImageMagick.
  - Needed to automatically generate the thumbnail images.
  - This won't be necessary with the next version of Lektor (3.4).
- [Install](https://www.getlektor.com/docs/installation/#install-pipx) pipx from the command line.
  - This program combines package management with environment isolation, and is designed for use with command line programs.
  - Make sure to close the command prompt window when done so that the `PATH` changes are recognized.
- [Install](https://www.getlektor.com/docs/installation/#finally-install-lektor) Lektor from the command line.
  - `pipx install lektor`

# Actions
## Previewing
- From the museum's `src` directory run `lektor server`.
  - This will automatically run an incremental build.
    - Whenever a source file is saved to the filesystem it will automatically be built and available for viewing locally.
  - View the website in the browser at <http://127.0.0.1:5000/>.
    - Note that trying to preview the website by simply opening the rendered `html` files in the browser won't work properly.

## Editing
- The `contents.lr` files are simply text files, and so can be edited (or created) in any text editor.
  - If `lektor server` is running, the ouput file will automatically be built when the source is saved.
- The source files can also be edited in the browser by clicking the edit icon in the upper right corner  of the preview page.

## Deploying
- From the museum's `src` directory run `lektor build`.
  - This only builds the files that have changed.
  - To force a clean build, first run `lektor clean`.
    - A clean build will be quite slow due to the size of the site, and will also cause the next deploy to be a full one (which is slow).
  - The build directory is a sibling of `src` called `build`.
- From the museum's `src` directory run `lektor deploy`.
  - Uses FTP.
    - Since FTP is stateless, Lektor uses a manifest file to keep track of changes to avoid having to transfer all the files
all the time. One consequence is that you shouldn't change the files (for example, with a separate FTP client) outside of
`lektor deploy`.
    - Note that the FTP username and password are hardcoded in the `glennsmuseum.lektorproject` file. See the 
[Lektor FTP documentation](https://www.getlektor.com/docs/deployment/ftp/) if you want to specify these on the command line instead.

# Description
## Source layout
Lektor projects have the following structure:
- The `content` directory contains the site content, organized in a directory tree, with individual page content being defined
in a structured *field / value* format in a file called `contents.lr`.
- The `models` directory contains the definitions of the models (i.e. the fields) used by the content.
- The `templates` directory contains the [Jinja](https://jinja.palletsprojects.com/en/3.1.x/) templates that show how to turn the structured content into an actual html file.
- The `assets` directory contains web assets that are copied directly into the generated output directory, without further processing.
- The root `.lektorproject` file and the `configs` directory define various settings.

Each museum item has its own source content file (`/src/content/items/[name]/contents.lr`). The fields are defined in `/src/models/item.ini`.
 - `name` is a short descriptive name that is used for the generated page's title.
 - `last-updated` indicates when the file was last updated. This is only used for ordering items in the RSS feed.
Specifically, when a new item is added with a new date, it will appear at the top of the RSS feed. The same thing will also happen
if you edit the date of an existing item, so only change the date if you explicitly want to alert everyone to the change. (For example, you wouldn't want to change the date just because you fixed a typo.)
 - `priority` is a floating point number that's only used for determining the order of items on a category page (the list of items, or the
everything page). We're using numbers between 0.0 and 10.0, though that's arbitrary.
 - `tags` is a comma-separated list of tags (content areas) that this item belongs to. For example, an IBM bombsight might have the `ibm`
and `bombsights` tags, causing the resulting item to appear under both categories in the generated site. These tags don't have to be
pre-defined anywhere, they're simply strings.
 - `figures` is a list of Figures that will accompany the item. There are three fields, separated by the `|` character.
   - The first is the figure identifier used in the caption. For example, a `1` will be rendered as `Figure 1`.
   - The second is the filename of the picture. For example, `bomb` will get turned into a reference to the `/pics/bomb.jpg` image
     (plus an automatically generated thumbnail version).
   - The third is the `alt` text that will be used for the image.
 - `description` is the [Markdown](https://www.markdownguide.org/basic-syntax/) text content that will be rendered on the item page. By convention this section starts with a `###` (i.e. `h3`)
header with the name of the device.

The top level navigation categories have their own directories
(as well as `everything` subdirectories). Each category file specifies the `tag` that it represents and has an optional `description`.
An index of the individual items marked with that tag will automatically be placed at the end of the generated file. Note that we
don't bother to create a category page for navigation targets (like the System 32) that contain only a single item, we just link
directly to that item in the navigation menu.

## Adding a new item
Here are the basic steps to add a new museum piece:
- Take photos.
- Copy each photo you want to use to the `/src/content/pics/` directory, making sure each one has a descriptive and (globally) unique name.
- Create a new directory in `/src/content/items/` for the new piece.
  - The name of the directory will be used for the URL.
- Copy the stub file `/src/item_stub.lr` to `contents.lr` in the new directory.
  - This isn't necessary, you could also just create a new empty `contents.lr` file and edit that. Using the stub just saves some time.
- Edit the new `contents.lr` file, using the field guidelines above.
- Preview the result in the browser.
  - Assuming that `lektor server` is running, the new page will automatically be built and available for preview.

## Notes
- A sitemap is automatically created as part of the build (and has been submitted to Google and Bing).
- The Atom (RSS) feed is automatically created as part of the build.

# Reference

- [Lektor documentation](https://www.getlektor.com/docs/)
- [Lektor source code](https://github.com/lektor/lektor)
- [List of Lektor plugins](https://www.getlektor.com/plugins/)
  - [lektor-thumbnail-generator](https://www.getlektor.com/plugins/lektor-thumbnail-generator/)
    - Used to automatically generate the thumbnails. 
  - [lektor-atom](https://www.getlektor.com/plugins/lektor-atom/)
    - Used to automatically generate the Atom (RSS) feed.
- [Markdown documentation](https://www.markdownguide.org/basic-syntax/)
- [Jinja documentation](https://jinja.palletsprojects.com/en/3.1.x/)
