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


Wget can interpret headers to name the file:

```
wget 127.0.0.1:8000 \
    --content-disposition \
    --post-data "m=This%20is%20the%20content.&t=Example"
```

A file name "Example.pdf" will be saved in the current directory.

# Note

Pandoc-server aims to give a service that can be link with other container to build little documents in PDF (export blog post, generate report, etc.)

This is not a service to build a full book or big document with a lot of latex extension. If you really need this, you may

- fork and modify docker-pandoc-server repository and adapt Dockerfile
- fork and modify pandoc-server and modify the python script (WSGI)
- include pandoc in you own project

