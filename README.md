# [ipynb2pelican](https://github.com/peijunz/ipynb2pelican) Plugin

The [ipynb2pelican](https://github.com/peijunz/ipynb2pelican) plugin implements `.ipynb` file format support to pelican.

### Features
+ Simply convert ipynb to html, no CSS is preserved
+ Uses preprocessors of nbconvert library
+ Provides cell-based summary generation
+ Uses first cell as the most natural and readable metadata

Writing metadata is as simple as writing metadata in markdown file.
```md
# This is title
+ date: 2020-02-22
+ tags: hello, world
```
> Hint: In jupyter notebook, press `Esc+M` will switch selected cell to markdown mode. 

It is recommended (but not required) to organize the metadata items
with Markdown bullets (`+`, `-` or `*`).
These will be stripped during metadata extraction,
but it keeps the metadata cell nicely readable in the notebook, like this:

### This is title
+ date: 2020-02-22
+ tags: hello, world

## Overview
The plugin is simple:

+ The CSS of jupyter will not be taken into outputs
+ The summary will be generated by Pelican

But it is still powerful and extensible:

+ A Solution for metadata
+ Syntax hightlight by nbconvert
+ Several configurable preprocessors provided
    - Metadata Extraction
    - SubCell Selection
    - Ignore cells with `#ignore` tag
    - Empty Cell Removal
+ You can change [preprocess.py](preprocess.py) and define your own preprocessors

## Preprocessors
Thanks to the preprocessor feature of nbconvert, useful preprocessors are built in this
project with on/off options. In your `pelicanconf.py`, you are able to
set [options](#options) to toggle preprocessors. You are encouraged to [share your own
preprocessors](https://github.com/peijunz/ipynb2pelican/wiki/Preprocessors)! 

Don't worry about preprocessors, switch off all options will have only 3% gain on
performance according to the test on my blog. So all of them are enabled by default.

### Metadata Extraction
As we stated, all metadata should be stored at the first cell of ipynb. If there is no metadata, then article is treated as draft and use filename as title and slug. After the extraction of metadata, the metdata cell will be removed, as we have extracted all the information. 

#### Summary Extration
By default, it will generate a summary of size 600. Every extra cell incurs a penalty of 120 chars in case that there are too many small cells. 

### Remove Empty Cells
Remove trivial cells without visible characters using regular expression `\S`

### Ignore some cells
You can include an `#ignore` comment at the beginning
of a cell of the Jupyter notebook to ignore it, removing it from the post content.

Note it is more strict than `#ignore` tag in pelican-ipynb. The purpose is to prevent kicking normal contents out of post content.

### SubCells Selection
The Subcells preprocessor is executed after Metadata preprocessor the metadate cell it self will be removed by Metadata preprocessor), so
zeroth cell is the first cell after metadata. The start and end should be written in the metadata like:
```md
# This is title
+ date: 2020-02-22
+ tags: hello, world
+ subcells: [5, -1]
```

The value will be evaluated by `start, end = ast.literal_eval(value)`. And then cells are sliced by `cells[start:end]`.

> Hint: If you want end to be infinity, use None

## Options

|Option Variable|Default|Meaning|
|------|-------|-------|
|`IPYNB_REMOVE_EMPTY`|True|Remove Empty Cells|
|`IPYNB_IGNORE`|True|Remove cells with `#ignore` tag at the beginning|
|`IPYNB_SUBCELLS`|True|Only preserve Subcells specified by `subcells: [begin, end)` metadata|
|`SUMMARY_SIZE` | 600 | Size of Summary|
|`CELL_PENALTY` | 120 | penalty of each cell in summary|

## Installation and Configuration
### Dependency
+ Both `python2`, `python3` are supported
+ `pelican`
+ `jupyter`
+ `ipython`
+ `nbconvert`

It has been tested on

+ `python 3.6+`
+ `pelican 4.5`
+ `jupyter/ipython/nbconvert 4.1`

### Installation
Download this repo and rename folder to `ipynb2pelican`. Then put it
into your plugins directory. A simpler way is by submodule this plugin:

```sh
git submodule add git@github.com:peijunz/ipynb2pelican.git pelican-plugins/ipynb2pelican
```

If your plugins are put
into `pelican-plugins` directory, the file tree
should looks like:
```
content/
pelicanconf.py
pelican-plugins/
└── ipynb2pelican
    ├── __init__.py
    ├── preprocess.py
    ├── reader.py
    └── README.md
```
### Configuration
In the `pelicanconf.py`:
```python
PLUGIN_PATH = ['pelican-plugins']       # Ensure your plugin path is in it
PLUGINS = ['ipynb2pelican']             # Name of the plugin
IGNORE_FILES = ['.ipynb_checkpoints']   # Prevent parsing checkpoints files
```

If you are not using `pelican-plugins`, you should change it accordingly.
