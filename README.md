How to Release a Python Package
===============================

The instructions and helper script here aid in properly building a Python
distribution package and releasing it to [PyPI][] (the Python Package
Index), whence it can be installed [pip] and similar tools.

Requirements:
- You must be using a `pyproject.toml` file to define your project information.
- You must have [`pactivate`] present in the same directory as
  `pyproject.toml` or in the parent directory. . (You do not actually need
  to be using pactivate for your project itself, though of course this is
  recommended!)

Currently this is mostly a manual process, but a little bit of it is
automated with the [`build-release`](./build-release) script in this repo.
That script can be run from any location, but it assumes that you're in the
root of your project (i.e., the directory containing the `pyproject.toml`
file).

The release files will be built under `.build/release/` in the same
directory as `pactivate`. This allows for repos with a single package or
repos with multiple distribution packages, each in a direct subdirectory of
the repo root. (In the latter case you will want to use a tag name that
includes the package name, rather than just `v0.0.0`.)


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


License Specification Formats
-----------------------------

[PEP 639] "Improving License Clarity with Better Package Metadata"
introduced a new format for the license information metadata. The old
`pypackage.toml` format is one of:

    license = { file = 'LICENSE' }
    license = { text = '…' }

The [new `pyproject.toml` format][pyprj-lic] (not quite the same as the
`METADATA` format in the PEP) uses an [SPDX license expression][spdx-expr]
with tokens from the [SPDX license list][spdx-list], and an optional
separate list of files that contain licenses.

    license = "MIT AND (Apache-2.0 OR BSD-2-Clause)"
    license-files = ["LICEN[CS]E*", "vendored/licenses/*.txt", "AUTHORS.md"]

The new format requires `setuptools >= 69` in order to be able to build the
package. (Note that this can cause tools like Tox, which don't just use but
build the package under with versions of setuptools, to choke.)

From about 2026-02 `setuptools` will stop supporting the old format and
until then builds will emit a lot of warnings if you're using the old
format. (PyPI seems likely to accept the old format for much longer.)

To help during the conversion period, `build-release` takes a `-L` option
which will suppress these warnings, allowing you to more easily see any
other warnings that appear.



<!-------------------------------------------------------------------->
[PyPI]: https://pypi.org/
[`pactivate`]: https://github.com/cynic-net/pactivate
[pip]: https://pypi.org/project/pip/

[PEP 639]: https://peps.python.org/pep-0639/
[pyprj-lic]: https://packaging.python.org/en/latest/guides/writing-pyproject-toml/#license
[spdx-expr]: https://packaging.python.org/en/latest/glossary/#term-License-Expression
[spdx-list]: https://spdx.org/licenses/
