SHTOOLS Release Procedure
-------------------------

1. Start by making all changes in the develop branch. First, make sure the version number (and other meta-data) is up-to-date in the following files:

    ```
    Makefile  # update shtools version number
    fpm/toml  # update shtools version number
    docs/_data/sidebars/mydoc_sidebar.yml  # update shtools version number
    docs/_data/sidebars/fortran_sidebar.yml  # update shtools version number
    docs/pages/mydoc/release-notes-v4.md  # update release notes
    docs/pages/fortran/fortran-release-notes-v4.md  # update release notes
    requirements.txt  # update version numbers of the python dependencies, if necessary
    requirements-dev.txt  # update version numbers of the python dependencies, if necessary
    environment.yml  # update version numbers of the python dependencies, if necesseary
    binder/environment.yml  # update version numbers of the python dependencies, as well as pyshtools version number
    cd docs; bundle update; cd ..
    ```

2. Update the documentation files:

    ```
    make remove-doc  # this ensures that the correct version number will be written to the fortran man pages
    make doc
    ```

3. Commit these changes to `develop`, make a pull request and merge develop into master.

4. Create a github release. Go to https://github.com/SHTOOLS/SHTOOLS/releases, create a tag of the form `vX.X`, and draft a new release. After this is done, a zipped archive will be sent to [Zenodo](https://doi.org/10.5281/zenodo.592762), which will create a doi for citation.

5. Update the master branch on your personal repo, along with the newly created tag:

    ```
    git checkout master
    git pull shtools master --tags # shtools is the name of the remote repo on github
    ```
    
6. Update pypi. For the next steps to work, the file ```.pypirc``` with the username and password needs to be set (see [this link](https://packaging.python.org/guides/migrating-to-pypi-org/#uploading)). ```pandoc``` needs to be installed with either ```conda install -c conda-forge pypandoc``` or ```pip install pypandoc```, and ```twine``` neeeds to be installed by ```pip install twine```. A pypi upload can only be done once for a given version. It is therefore recommended to test it first on pypitest.
    ```
    python3 setup.py sdist
    gpg --detach-sign -a dist/pyshtools-x.x.tar.gz
    twine upload dist/* -r pypitest
    pip3 uninstall pyshtools
    # Inspect the pypi page at https://test.pypi.org/project/pyshtools/4.7/ for errors in the project description or metadata
    pip3 install -i https://test.pypi.org/simple pyshtools --no-binary pyshtools
    ```
    Then, in a directory that is different from your local pyshtools repo, run a couple of quick tests. If everything works, you can then upload everything to pypi:
    ```
    python3 setup.py sdist
    gpg --detach-sign -a dist/pyshtools-x.x.tar.gz
    twine upload dist/* -r pypi
    # As a sanity check, inspect the pypi project page at https://pypi.org/project/pyshtools/
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

9. Update the web documentation on shtools.oca.eu using private gitlab repo. 

10. Update the version number for the **next** release in the `develop` branch

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
