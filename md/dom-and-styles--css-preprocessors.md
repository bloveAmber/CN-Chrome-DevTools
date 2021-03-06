Working with CSS Preprocessors
==

#### **Contents**

*   [Requirements](#toc-requirements)
*   [ Enabling CSS source maps ](#toc-enabling-css-source-maps)
*   [ Using Sass with CSS source maps ](#toc-using-css-source-maps)
*   [CSS preprocessor support](#toc-css-preprocessor-support)
*   [ How CSS source maps work ](#toc-how-css-source-maps-work)
*   [Resources](#toc-resources)

Many developers generate CSS style sheets using a CSS preprocessor, such as Sass, Less, or Stylus. Because the
CSS files are generated, editing the CSS files directly is not as helpful.

For preprocessors that support CSS source maps, DevTools lets you live-edit
your preprocessor source files in the Sources panel, and view the results without having to leave DevTools or refresh the page. When you inspect an element whose styles are provided by a generated CSS file, the Elements panel displays a link to the original source file,  not the generated .css file.

<figure class="screenshot"> ![Elements panel showing .scss stylesheet](css-preprocessors-files/sass-debugging.png)</figure>

To jump to the source file: 

*   Click the link to open the (editable) source file in the Sources panel.
*   **Control+click** (or **Command+click**) on any CSS property name or value to open the source file and jump to the appropriate line.

<figure class="screenshot">![Sources panel showing .scss file](css-preprocessors-files/sass-sources.png)</figure>

When you save changes to a CSS preprocessor file in DevTools, the CSS preprocessor should re-generate the
CSS files. Then DevTools reloads the newly-generated CSS file. 

<aside class="note">Changes made in an _external_ editor are not detected by DevTools until the Sources tab
containing the associated source file regains focus. Also, manual editing of a CSS file generated by the Sass/LESS/other compiler will break the source map association until the page is reloaded.</aside>

<aside class="note">If you're using Workspaces, you'll need to make sure the generated CSS file is also mapped into the workspace. You can verify this by looking in Sources panel right-side tree, and seeing the CSS is from your local folder.</aside>

## Requirements

There are a few requirements to note when working with a CSS preprocessor:

*   To use this workflow, your CSS preprocessor must support CSS source maps, specifically the Source Map v3 proposal. The CSS source maps must be built
along with the CSS files, so DevTools can map each CSS property to the correct location in the original source file
(for example, .scss file).

*   For the DevTools to automatically reload styles when you change the source file, your preprocessor must be set up to
regenerate CSS files whenever a source file changes.  Otherwise, you must regenerate CSS files manually and reload the
page to see your changes.

*   You must be accessing your site or app from a web server (not a **file://** URL), and
the server must serve the CSS files as well as the source maps (.css.map) and source files (.scss, etc.).

*   If you are _not_ using the Workspaces feature, the web server must also supply the
`Last-Modified` header. The Python `SimpleHTTPServer` module provides this header by default.
You can start a web server to serve the contents of the current directory like this:

    <pre class="prettyprint">python -m SimpleHTTPServer</pre>

## Enabling CSS source maps

CSS source maps are enabled by default. You can choose to
enable automatic reloading of generated CSS files. 

**To enable CSS source maps and CSS reload:**

1.  Open DevTools Settings and click **General**.

2.  Turn on **Enable CSS source maps** and **Auto-reload generated CSS**.

## Using Sass with CSS source maps

To live-edit Sass files in Chrome you need to have the [pre-release version of the Sass compiler](http://sass-lang.com/download.html), which is the only version that currently supports source map generation.

<pre class="prettyprint">gem install sass -v '>=3.3.0alpha' --pre</pre>

Once Sass is installed, start the Sass compiler to watch for changes to your Sass source files and create source map files for each generated CSS file, for example: 

<pre class="prettyprint">sass --watch **--sourcemap** sass/styles.scss:styles.css</pre>

If you are using Compass, note that Compass doesn’t yet support the pre-release version of Sass, so you can’t use Sass debugging with Compass.

## CSS preprocessor support

DevTools supports the [Source Map Revision 3 proposal](https://docs.google.com/a/google.com/document/d/1U1RGAehQwRypUTovF1KRlpiOFze0b-_2gc6fAH0KY0k/edit#). This proposal is being implemented in several CSS preprocessors:

*   Sass: As described above, this is supported in the prerelease version of Sass.
*   Compass: [Implemented in compass 1.0.0.alpha.16.](https://github.com/chriseppstein/compass/blob/master/compass-style.org/content/CHANGELOG.markdown#100alpha16-12052013)
<li>Less: Implemented in 1.5.0. See [issue #1050](https://github.com/less/less.js/issues/1050#issuecomment-25566463) for details and usage patterns.
*   Autoprefixer: Implemented in 1.0. [Autoprefixer docs](https://github.com/ai/autoprefixer#source-map) explain how to use it, along with absorbing an input sourcemap (from another preprocessor).
*   Libsass: [Implemented](https://github.com/hcatlin/libsass/commit/366bc110c39c26c9267a1cc06e578beb94cd93ef).
<li>Stylus: Under consideration. See [issue #1072](https://github.com/LearnBoost/stylus/issues/1072).

## How CSS source maps work

For each CSS file it produces, the preprocessor generates a [source map](http://www.html5rocks.com/en/tutorials/developertools/sourcemaps/) file (.map file) in addition to the compiled CSS. The source map file is a JSON file that
defines a mapping between each generated CSS declaration and the corresponding line of the source file. Each CSS file contains an annotation that specifies the URL of its source map file, embedded in a special comment on the
last line of the file:

<pre class="prettyprint">
/*# sourceMappingURL=&lt;url&gt; */
</pre>

For instance, given an SCSS file named **styles.scss**:

<pre class="prettyprint lang-css">
$textSize: 26px;
$fontColor: red;
$bgColor: whitesmoke;

h2 {
    font-size: $textSize;
    color: $fontColor;
    background: $bgColor;
}
</pre>

Sass generates a CSS file, **styles.css**, with the sourceMappingURL annotation:

<pre class="prettyprint lang-css">
h2 {
  font-size: 26px;
  color: red;
  background-color: whitesmoke;
}
/*# sourceMappingURL=styles.css.map */
</pre>

Below is an example source map file:

<pre class="prettyprint">
{
  "version": "3",
  "mappings":"AAKA,EAAG;EACC,SAAS,EANF,IAAI;EAOX,KAAK"
  "sources": ["sass/styles.scss"],
  "file": "styles.css"
}
</pre>

## Resources

Many users have developed their own workflows using CSS preprocessors.
See the following external articles for tutorials and alternate workflows.

*   [Getting started with CSS sourcemaps and in-browser Sass editing](https://medium.com/what-i-learned-building/b4daab987fb0)
*   [Faster Sass debugging and style iteration with source maps, Chrome Web Developer Tools and Grunt](http://benfrain.com/add-sass-compass-debug-info-for-chrome-web-developer-tools/)

<aside class="note">**Note:** External resources may not reflect the latest version of Chrome.</aside>