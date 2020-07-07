# OmniSciDB Developer Documentation

Documentation is available at:

https://www.mapd.com/docs/

## Sphinx docs

[Sphinx](http://www.sphinx-doc.org) is a python-based tool to generate documentation. Here it will be used to generate HTML pages with the OmniSciDB Developer Documentation.

## Building docsd

Documentation can be build locally using a make target on the host machine, using a docker container, or manually.

* In the below steps, replace `make html` with `make livehtml` to have the build watch for changes and provide a live-preview.

#### Building with make target

There is a make target, `make sphinx` that can be ran from the top-level `../build/` directory after initialized with `cmake`.

### Building with Docker

Docker can be used to build the documentation locally without installing any dependencies to the host system. An internal container is available with the name: `docker-internal.mapd.com/mapd/sphinx-doc`. 

To build the docs using the available container, from inside this `docs` directory run:

```
docker run --rm -v $PWD:/doc -e USER_ID=$UID docker-internal.mapd.com/mapd/sphinx-doc make html
```

If there are any changes to dependencies, a new container image can be built using the `Dockerfile` in this directory.

To build a new version of the `sphinx-doc` container, from inside this `docs` directory run:


```
docker build -t sphinx-doc:<version> .
```

Where <version> is any unique version number. Proceed with the above step and replace `docker-internal.mapd.com/mapd/sphinx-doc` with `sphinx-doc:<version>` to build the docs with the updated dependencies.


### Building Manually

#### Requirements

Sphinx requires Python 3 (tested on Python 3.7) and the required python packages are installed with pip. See requirements.txt for list of required packages.

#### Building Sphinx docs

This will take the source docs files from the `./source/` directory and output HTML site files into the `./build/` directory.

#### Manually

The following steps use a python virtual environment:

```
python3 -m venv sphinx-env
. sphinx-env/bin/activate
pip install -r requirements.txt
make html
deactivate
```

### Previewing Locally

Once the docs are built, running `python -m http.server` from the `docs/build/html` directory will
allow for viewing the docs at `localhost:8000`.

#### VSCode Live Preview

Install the [reStructuredText extension](https://github.com/vscode-restructuredtext/vscode-restructuredtext)

Point `settings.json` to the the correct Python path. 
Assuming the manual using `sphinx-env` virtualenv from above:

```json
{
    "python.pythonPath": "${workspaceFolder}/docs/sphinx-env/bin/python"
}
```

`reStructuredText: Open Locked Preview to the Side` will give a live preview window with the generated sphinx docs.

