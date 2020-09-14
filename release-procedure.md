SHTOOLS Release Procedure
-------------------------

1. Start by making all changes in the develop branch. First, make sure the version number (and other meta-data) is up-to-date in the files

    ```
    Makefile  # update version number
    docs/_data/sidebars/mydoc_sidebar.yml  # update version number
    docs/_data/sidebars/fortran_sidebar.yml  # update version number
    docs/pages/mydoc/release-notes-v4.md  # update release notes
    docs/pages/mydoc/fortran-release-notes-v4.md  # update release notes
    requirements.txt  # update version numbers of python dependencies
    requirements-dev.txt  # update version numbers of python dependencies
    environment.yml  # update version numbers of python dependencies
    binder/environment.yml  # update version numbers of python dependencies, as well as pyshtools version number
    cd docs; bundle update; cd ..
    ```

2. Update the documentation files:

    ```
    make remove-doc  # this ensures that the correct version number is written to the fortran man pages
    make doc
    ```

3. Commit these changes to `develop`, make a pull request and merge develop into master.

4. Create a github release. Go to https://github.com/SHTOOLS/SHTOOLS/releases, create a tag of the form `vX.X`, and draft a new release. After this is done, a zipped archive will be sent to [Zenodo](https://doi.org/10.5281/zenodo.592762), which will create a doi for citation.

5. Update the master branch on your personal repo, along with the newly created tag:

    ```
    git checkout master
    git pull shtools master --tags # shtools is the name of the remote repo on github
    ```
    
6. Update pypi. For the next steps to work, the file ```.pypirc``` with the username and password needs to be set (see [this link](https://packaging.python.org/guides/migrating-to-pypi-org/#uploading)). Also ```pandoc``` needs to be installed with either ```conda install -c conda-forge pandoc pypandoc``` or ```pip install pypandoc```. A pypi upload can only be done once for a given version. It is therefore recommended to test it first on pypitest.
    ```
    python3 setup.py sdist
    gpg --detach-sign -a dist/pyshtools-x.x.tar.gz
    twine upload dist/* -r pypitest
    pip3 uninstall pyshtools
    pip3 install -i https://test.pypi.org/pypi pyshtools --no-binary pyshtools
    ```
    If this works, you can do the same thing for pypi:
    ```
    python3 setup.py sdist
    gpg --detach-sign -a dist/pyshtools-x.x.tar.gz
    twine upload dist/* -r pypi
    pip3 uninstall pyshtools
    pip3 install pyshtools
    ```

7. Build the wheels:

    ```
    git clone https://github.com/shtools/build-shtools.git # only necessary the first time.
    cd build-shtools
    git submodule update --remote  # add the option --init the first time you use this command.
    git commit -a -m "Update shtools and multibuild to master"
    git push
    ```

8. Update the homebrew installation by editing the file shtools.rb in the homebrew-shtools repo. First, change "url" to point to the new version (the link can be found on the release page). Then, download the file the url points to, determine its SHA256 hash using "shasum -a 256 filename", and update the SHA256 hash. Finally,

    ```
    git add -u
    git commit
    git push
    ```

9. Update the version number for the **next** release in the `develop` branch

    ```
    git checkout develop
    # change version numbers for the files
    Makefile
    docs/_data/sidebars/mydoc_sidebar.yml
    docs/_data/sidebars/fortran_sidebar.yml
    git add -u
    git commit -m 'Update version numbers on develop for next release cycle'
    git push
    ```
