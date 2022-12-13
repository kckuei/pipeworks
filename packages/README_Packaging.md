# Packaging with Python

## Version History

* 0.0.2 - Initial package with `hatchling`.
* 0.0.3 - Switch to `setuptools` and add package version depenencies. Added Anaconda as option.


## Packaging and Building with PyPI (pip install)
### References
* [PyPI Packaging Tutorial](https://packaging.python.org/en/latest/tutorials/packaging-projects/)
* [Pip Documentation: pyproject.toml](https://pip.pypa.io/en/stable/reference/build-system/pyproject-toml/)

### Steps
1. Prepare the package directory structure as follows:
```
packaging_tutorial/
├── LICENSE
├── pyproject.toml
├── README.md
├── src/
│   └── pipeworks/
│       └── __init__.py
└── tests/
```

This structure is slightly different from the one shown in the tutorial. Note that `pipeworks.py` is renamed `__init__.py` so that we can directly import as
`import pipeworks as pw` instead of as `from pipeworks import pipeworks`.

2. Configure `pyproject.toml` from starter file.
 
```toml
[build-system]
requires = ["setuptools>=61.0",
		    "matplotlib>=3.4.3",
		    "numpy>=1.20.3",
		    "scipy>=1.7.1",
		    "pandas>=1.3.4"]
build-backend = "setuptools.build_meta"

[project]
name = "pipeworks"
version = "0.0.3"
authors = [
  { name="Kevin Kuei", email="@gmail.com" },
]
description = "Collection of classes for calculating pressurized and open channel flow hydraulics for outlet works. "
readme = "README.md"
requires-python = ">=3.9.7"
classifiers = [
    "Programming Language :: Python :: 3",
    "License :: OSI Approved :: MIT License",
    "Operating System :: OS Independent",
]

[project.urls]
"Homepage" = "https://github.com/kckuei/pipeworks"
"Bug Tracker" = "https://github.com/kckuei/pipeworks/issues"


```

3. Enter the commands in the package directory to build and upload production package:
```
python3 -m build
python3 -m twine upload dist/*
```

4. Enter the PyPI account username and password.

5. Install the package with:
```
python3 -m pip install pipeworks
```

To uninstall a previous version:
```
python3 -m pip uninstall pipeworks
```

6. Verify the installation:
```
python3
import pipeworks as pw
help(pw)
```

### Specifying Dependencies

If we want to set the dependencies explicitly, e.g. with `setuptools`:
```toml
[build-system]
requires = ["setuptools>=61.0",
	    "matplotlib>=3.4.3",
	    "numpy>=1.20.3",
	    "scipy>=1.7.1",
	    "pandas>=1.3.4"]
build-backend = "setuptools.build_meta"
```

## Packaging and Building with Anaconda (conda install)
### References
* [Building conda from scratch](https://conda.io/projects/conda-build/en/latest/user-guide/tutorials/build-pkgs.html)
* [Building conda package with conda skeleton](https://conda.io/projects/conda-build/en/latest/user-guide/tutorials/build-pkgs-skeleton.html)
* [Conda Metadata (meta.yaml)](https://docs.conda.io/projects/conda-build/en/latest/resources/define-metadata.html)
* [Conda Package Specification (Dependencies)](https://docs.conda.io/projects/conda-build/en/latest/resources/package-spec.html#build-version-spec)
* [Generating `meta.yaml` with grayskull](https://github.com/conda/conda-build/issues/4313)


### Steps
Uses the optional [PyPi as source](https://conda.io/projects/conda-build/en/latest/user-guide/tutorials/build-pkgs.html) when generating a package for Anaconda. 

1. Configure the starter conda recipe (`meta.yaml`) file:

```yaml
{% set name = "pipeworks" %}
{% set version = "0.0.3" %}


package:
  name: {{ name|lower }}
  version: {{ version }}

source:
  url: https://pypi.io/packages/source/{{ name[0] }}/{{ name }}/pipeworks-{{ version }}.tar.gz
  sha256: 444273712fd628c53f94644cd2ffa96815adb8810907cf989a7b61dcf26b59fb

build:
  number: 0
  noarch: python
  script: {{ PYTHON }} -m pip install . -vv

requirements:
  host:
    - pip
    - python >=3.9
  run:
    - python >=3.9
    - matplotlib >=3.4
    - numpy >=1.2
    - scipy >=1.7
    - pandas >=1.3

test:
  imports:
    - pipeworks
  commands:
    - pip check
  requires:
    - pip

about:
  home: https://pypi.org/project/pipeworks/
  summary: Collection of classes for calculating pressurized and open channel flow hydraulics for outlet works.
  license: MIT
  license_file: LICENSE

extra:
  recipe-maintainers:
    - kckuei
```

The sha256 hash can be found in the sidebar under *[download files](https://pypi.org/project/pipeworks/#files)*.


Alternatively, the `meta.yaml` file can also be automatically generated from the source PyPI repository using `grayskull`. To install `grayskull`, enter:
```
conda install -c conda-forge grayskull

```

Then generate the `meta.yaml` from PyPI directly.
```
grayskull pypi pipeworks
```

This creates a new folder with the package name and the `meta.yaml` inside it. 

`NOTE`: Any dependencies of the package **must** be added in the `meta.yaml` file under  `requirements:run` before building. See [Conda Metadata](https://docs.conda.io/projects/conda-build/en/latest/resources/define-metadata.html) and [Package Specification](https://docs.conda.io/projects/conda-build/en/latest/resources/package-spec.html#build-version-spec) for more details.

2. Once the recipe file is ready, use conda-build to create the package locally:
```
conda build pipeworks
```

3. When the conda-build is finished, it displays the location of the package, e.g.:
```
 /home/kckuei/anaconda3/conda-bld/noarch/pipeworks-0.0.3-py_0.tar.bz2
```

Any intermediate/incomplete builds can be removed by the following:
```
conda build purge
```

4. Upload the package to Anaconda.org. Make sure anaconda client is installed:
```
conda install anaconda-client
```

5. Then log into the Anaconda account with username and credentials:
```
anaconda login
```

6. Upload the package:
```
anaconda upload /home/kckuei/anaconda3/conda-bld/noarch/pipeworks-0.0.3-py_0.tar.bz2
```

### Additional Notes
* If 'Solving Environment' stalls/hangs with conda when installing packages, try creating a new environment as conda seems to dislike resolving on `base` and in `monolithic` environments.
* [Conda Cheat Sheet](https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf)

To show the available environments:
```
conda env list
```

To create a new environment and activate it:
```
conda create --name py37 python=3.7
conda activate py37
```

To remove an environment:
```
conda env remove --py37
```

To produyce a spec list of packages:
```
conda list --explicit
```

We can pipe it to a text file:
```
conda list --explicit > backup.txt
```

To use the spec file to create an identical environment on the same machine or another machine:
```
conda create --name myenv --file backup.txt
```

To use the spec file to install its listed packages into an existing environment:
```
conda install --name myenv --file backup.txt
```
