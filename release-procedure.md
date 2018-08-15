SHTOOLS Release Procedure
-------------------------

1. Start by making all changes in the develop branch. First, make sure the version number (and other meta-data) is up-to-date in the files

    ```
    Makefile
    VERSION
    pyshtools/__init__.py
    docs/_data/sidebars/mydoc_sidebar.yml
    docs/pages/mydoc/release-notes-v4.md # go to zenodo and click on new version to see the reserved doi.
    ```

2. Update the documentation and notebook html files:

    ```
    make remove-doc # this ensures that the correct version number is written to the man pages
    make doc
    make notebooks
    ```

3. Commit these changes to `develop`, merge develop into master, change ISRELEASED to true, and tag as new version:

    ```
    git push origin develop
    git checkout master
    git merge develop
    # change ISRELEASED to True in setup.py
    git add -u
    git commit -m 'Change ISRELEASED to True'
    git push origin master
    git tag -s vX.X -m "Version X.X"
    git push origin master vX.X
    ```

4. Create static web pages for use on a different server

    ```
    make www
    git checkout www
    cp -r www/* .
    rm -r www
    git add -u
    git commit -m "Update web documentation for vX.X"
    git push
    ```

5. Create a github release. Go to https://github.com/SHTOOLS/SHTOOLS/releases and draft a new release. After this is done, a zipped archive will be sent to Zenodo, which will create a doi for citation. With this citation, you can then update the github release notes. 

6. Update pypi. For the next steps to work, the file ```.pypirc``` with the username and password needs to be set (see [this link](https://packaging.python.org/guides/migrating-to-pypi-org/#uploading)). Also ```pandoc``` needs to be installed with either ```conda install -c conda-forge pandoc pypandoc``` or ```pip install pypandoc```. A pypi upload can only be done once for a given version. It is therefore recommended to test it first on pypitest.
    ```
    python setup.py sdist
    gpg --detach-sign -a dist/pyshtools-x.x.tar.gz
    twine upload dist/* -r pypitest
    pip uninstall pyshtools
    pip install -i https://test.pypi.org/pypi pyshtools --no-binary pyshtools
    ```
    If this works, you can do the same thing for pypi:
    ```
    python setup.py sdist
    gpg --detach-sign -a dist/pyshtools-x.x.tar.gz
    twine upload dist/* -r pypi
    pip uninstall pyshtools
    pip install pyshtools
    ```

7. Build the wheels:

    ```
    git clone https://github.com/shtools/build-shtools.git
    cd build-shtools
    git submodule update --init --remote
    git commit -a -m "Update shtools and multibuild to master"
    git push
    ```

8. Update the homebrew installation by editing the file shtools.rb in the homebrew-shtools repo. First, change "url" to point to the new version (the link can be found on the release page). Then, download the file the url points to, determine its SHA256 hash using "shasum -a 256 filename", and update the SHA256 hash. Finally,

    ```
    git add -u
    git commit
    git push
    ```

9. Update the documentation at shtools.oca.eu by copying the folder www.

10. Update the version number for the **next** release in the `develop` branch, anc change ISRELEASED to False in `setup.py`

    ```
    git checkout develop
    # change version numbers for the files
    Makefile
    VERSION
    pyshtools/__init__.py
    docs/_data/sidebars/mydoc_sidebar.yml
    # change ISRELEASED to False in setup.py
    git add -u
    git commit -m 'Change ISRELEASED to False'
    git push
    ```
