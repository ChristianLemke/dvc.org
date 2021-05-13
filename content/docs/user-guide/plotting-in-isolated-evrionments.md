# Plotting in isolated environments

There are cases when user does not have connection to the internet. For example,
for security reasons. In such cases, `dvc plots` - generated HTML will not
render properly. In this guide we show how to fix that.

When using [plots](/doc/commands-reference/plots), DVC will generate HTML file
that contains metrics visualizations. What lets user see the plots in the
browser is a piece of javascript that uses
[Vega-Lite](https://vega.github.io/vega-lite/) library. If we inspect any HTML
file generated using `dvc plots` command we can see that required libreries are
downloaded from web:

```html
<script src="https://cdn.jsdelivr.net/npm/vega@5.10.0"></script>
<script src="https://cdn.jsdelivr.net/npm/vega-lite@4.8.1"></script>
<script src="https://cdn.jsdelivr.net/npm/vega-embed@6.5.1"></script>
```

DVC provides a way to configure custom HTML that can help avoiding accessing the
internet and customize the plots.

## Example: using cutsom HTML with downloaded Vega libraries

In order to provide the libraries we need to download them, and point our plots
file where they can be found. In this example, we will download the libraries
and store them in our repository:

```bash
wget https://cdn.jsdelivr.net/npm/vega@5.10.0 -O my_vega.js
wget https://cdn.jsdelivr.net/npm/vega-lite@4.8.1 -O my_vega_lite.js
wget https://cdn.jsdelivr.net/npm/vega-embed@6.5.1 -O my_vega_embed.js
```

Now lets create our custom HTML document:

```bash
echo '<html>
<head>
    <script src="my_vega.js" type="text/javascript"></script>
    <script src="my_vega_lite.js" type="text/javascript"></script>
    <script src="my_vega_embed.js" type="text/javascript"></script>
</head>
<body>
	{plot_divs}
</body>
</html> ' > page_template
```

Here we are creating a HTML-like document, with one difference - `{plots_divs}`
marker. It tells DVC where to put plots generated with `dvc plots`.

Now, if we use `dvc plots` command with `--html-template page_template` option,
DVC will use `page_template` and create HTML visualization file basing on it.

One can permanently set the new template to be a default with following config
command:

`dvc config plots.html_template page_template`

Note, that the path has to be relative to `.dvc` directory.

_Note: To make HTML file accessible to all users in your organization, the paths
in HTML document need to be absolute. It is worth to consider putting the libs
on a server in oganization network, to make sure all users have access to them._
