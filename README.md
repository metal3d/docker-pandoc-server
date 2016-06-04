# docker-pandocserver

Launch a pandoc-server to build pdf using HTTP POST requests.

# How to

Pull the docker image:

```
docker pull metal3d/pandoc-server
```

Launch a container

```
docker run -it -p 8000:8000 --rm metal3d/pandoc-server
```

Now you can send POST data in `:8000` port. Container will send you a named pdf built from pandoc.

# POST vars

These HTTP POST vars are used:

- "m": the content markdown (pandoc compatible)
- "t": title used to name you pdf file
- "hl": highlight theme to use (eg.zenburn)

Example:

```
curl -X POST 127.0.0.1:8000 \
    -d "m=This%20is%20the%20content.&t=Example"
```

This command returns the pdf content in STDOUT. 

You may now pass file:

```
curl -X POST 127.0.0.1:8000 \
    -F "m=@README.md"
```

And pass template tar file + template to use:

Wget can interpret headers to name the file:

```
wget 127.0.0.1:8000 \
    --content-disposition \
    --post-data "m=This%20is%20the%20content.&t=Example"
```

A file name "Example.pdf" will be saved in the current directory.


# POST data and templates

Each value is sent as POST forma data:

- "m": markdown content or file
- "t": title that will be used to name the returned pdf file
- "hl": default is None, give the hightlight theme for syntax hightlight blocks
- "tpl": give the template file to use (latex only for now), this file should be contained at root at the tarball
- "tar": tarball file containing template, images, sty file and so on. 

Note that the server will untar the template archive and change working dir to the root of the tarball content, so you may tar you template folder at root.

For exemple, your tempalte is contained in "your-folder":

```
your-folder/
    mytemplate.tex
    images/
      img1.png
      img2.png
    my.sty
```
You should do:

```bash
$ cd ..
$ tar cfz your-template.tgz -C your-folder .
```

That way, the tarball contains files at root:

```bash
$ tar tf your-template.tgz
mytemplate.tex
images/
  img1.png
  img2.png
my.sty
```

And you can now try:

```bash
curl -X POST \
    -F "tar=@your-template.tgz" \
    -F "tpl=mytemplate.tex" \
    -F "m=@my-markdown-content.md" > out.pdf
```

# Note

Pandoc-server aims to give a service that can be link with other container to build little documents in PDF (export blog post, generate report, etc.)

This is not a service to build a full book or big document with a lot of latex extension. If you really need this, you may

- fork and modify docker-pandoc-server repository and adapt Dockerfile
- fork and modify pandoc-server and modify the python script (WSGI)
- include pandoc in you own project

