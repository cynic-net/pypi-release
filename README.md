How to Release a Python Package
===============================

The instructions and helper script here aid in properly building a Python
distribution package and releasing it to [PyPI][] (the Python Package
Index), whence it can be installed [pip] and similar tools.

Requirements:
- You must be using a `pyproject.toml` file to define your project
  information.
- You must have [`pactivate`] present in the same directory as
  `pyproject.toml` to build a virtual environment. (You do not actually
  need to be using pactivate for your project itself, though of course this
  is recommended!)

Currently this is mostly a manual process, but a little bit of it is
automated with the [`build-release`](./build-release) script in this repo.
That script can be run from any location, but it assumes that you're in the
root of your project (i.e., the directory containing the `pyproject.toml`
and `pactivate` files).


Release Process
---------------

1. Update the following files:
   - `pyproject.toml`: Remove the `.devN` suffix from the version number.
     (Lack of a `.devN` suffix indicates that a developer didn't add it
     when making a releasable change; in this case just bump the version
     number.)
   - `doc/CHANGELOG.md`: Rename the 'dev' section to the new version number
     and date of release, and add a new (empty) 'dev' section to the top of
     the list of releases.
   - Commit these changes to `main`, but do not push them up to GitHub yet.
     Generally the commit should not include any other changes, and the
     commit message can be just 'Release x.y.z'.

2. Build and check the release.
   - Change the current working directory to the project root.
   - Run `build-release`, which will do a few checks of the configuration
     (these are far from comprehensive), create/activate the `pactivate`
     virtualenv, install `build` and `twine`, and run `pyproject-build`
     and `twine check`.
   - Fix anything broken.

3. Upload the release
   - `git tag v0.x.x`
   - `git push r main tag v0.x.x`   # (replace `r` with your remote name)
   - `build-release -u`             # (ensure you have your API token handy)


Versioning Notes
----------------

`.devN`/`.betaN`/etc. versions can be uploaded to PyPI, but these will be
marked as "pre-release." They will show up at the top of the release
history, but the "latest version" shown on the top page of the project and
installed with `pip install dent` will be unchanged, and the pages for the
pre-release versions will have a warning icon and "Stable version
available" button. Pre-release versions can be installed from PyPI if asked
for explicitly: `pip install 'dent@1.0.0.dev3'`.



<!-------------------------------------------------------------------->
[PyPI]: https://pypi.org/
[`pactivate`]: https://github.com/cynic-net/pactivate
[pip]: https://pypi.org/project/pip/
