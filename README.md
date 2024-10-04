# NMFS JupyterHub base image

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/nmfs-opensci/base-hub-image/HEAD)
[![Build and push container image](https://github.com/nmfs-opensci/base-hub-image/actions/workflows/build.yaml/badge.svg)](https://github.com/nmfs-opensci/hub-image/actions/workflows/build.yaml)

The JupyterHub docker image used for [nmfs-openscapes.2i2c.cloud](https://nmfs-openscapes.2i2c.cloud/hub/login?next=%2Fhub%2F)
hosted on https://gh.io/repository/nmfs-opensci/base-hub-image. The repo is based off the [CryoCloud hub-image](https://github.com/CryoInTheCloud/hub-image).

The image is built with [repo2docker](https://repo2docker.readthedocs.io), which uses
Ubuntu Bionic Beaver (18.04) as the base image. 

## Updating packages in this repository

You can add or update packages on the NMFS Openscapes hub by making pull requests to this
repository. Follow these steps:

1. Edit either the [`environment.yml`](https://github.com/nmfs-opensci/base-hub-image/edit/main/environment.yml)
   file (for most Python packages), [`apt.txt`](https://github.com/nmfs-opensci/base-hub-image/edit/main/apt.txt)
   file (for packages that need to be used in the Linux Desktop environment in the cloud) or [`appendix`](https://github.com/nmfs-opensci/base-hub-image/edit/main/appendix) (for software that needs to be installed at the root level).
2. Start a [pull request](https://github.com/nmfs-opensci/base-hub-image/pulls). A bot will comment with a link to
   [mybinder.org](https://mybinder.org) where you can test your pull request to make sure it works
   as you would expect.

### Testing locally

To test the build locally, first ensure you have an up-to-date conda lock file, then
build with `repo2docker` (if your conda lock file was already updated by the bot as
described above, you can skip the first line):

```
conda-lock lock --mamba --kind explicit --file environment.yml --platform linux-64
repo2docker --appendix "$(cat appendix)" .
```

This build may take up to 30 minutes.

Once the image is built, `repo2docker` will automatically run a JupyterLab
server and display a message like this:

```
    To access the notebook, open this file in a browser:
        file:///home/<YOUR_USERNAME>/.local/share/jupyter/runtime/nbserver-27-open.html
    Or copy and paste this URL:
        http://127.0.0.1:53695/?token=<YOUR_TOKEN>
```

Click the URL on the last line of the `repo2docker` output to open the local JupyterLab
instance in your browser, and you're ready to test!

From here, you'll be able to locally test anything you can do in a cloud deployment:
run terminal commands, edit and run notebooks, or start a desktop VNC session.

## Updating the NMFS Openscapes JupyterHub to use a new image

After your PR gets merged, our GitHub Actions will build and push a new image to 
our image repository.

Once a new tag appears, someone with JupyterHub Admin permissions on the CryoCloud hub will have to
update the system to use the new image. With the new `Other` image selector, an admin will need to test 
the image and then ask a 2i2c engineer to update the image by sending them the tag name.

1. When starting the hub, select `Other` from the `Image` selector.
2. Add the tag of the image you want under `Custom` to use the new tag pushed for the PR tags page. Make sure there are
   no trailing spaces!
3. Hit 'Start'. If there is a bug in your image, the hub may not start up and will show error messages. The image will need to be fixed if this occurs.
4. Make sure all of the new tools are present and test that your imports work properly in a notebook.
5. The admin can then send an email to 2i2c with the tag we want added to the hub.
