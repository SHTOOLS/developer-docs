SHTOOLS Release Procedure
-------------------------

1. Start by making all changes in the develop branch

    ```
    git checkout develop
    ```

2. Make sure the version number is up-to-date in the files

    ```
    Makefile
    VERSION
    index.html
    pyshtools/__init__.py
    www/history.html
    ```

4. Update the documentation and notebook html files:

    ```
    make doc
    make notebooks
    ```

5. Change ISRELEASED to True in `setup.py`:

    ```
    git add -u
    git commit -m 'Change ISRELEASED to True'
    ```
    
6. Merge develop into master and tag as new version:

    ```
    git checkout master
    git merge develop
    git push
    git tag -s vX.X -m "Version X.X"
    git push origin vX.X
    ```

7. Update gh-pages

    ```
    git checkout gh-pages
    git checkout master index.html
    git checkout master www
    git add -u
    git commit -m "Update web documentation for vX.X"
    git push
    ```

8. Create a github release. Go to https://github.com/SHTOOLS/SHTOOLS/releases and draft a new release. After this is done, a zipped archive will be sent to Zenodo, which will create a doi for citation. With this citation, you can then update the github release notes. 

9. Update pypi. For the next steps to work, the file ```.pypirc``` with the username and password needs to be set (e.g. http://peterdowns.com/posts/first-time-with-pypi.html). Also ```pandoc``` needs to be installed with ```conda install -c conda-forge pandoc pypandoc```. A pypi upload can only be done once for a given version. It is therefore recommended to test it first on pypitest.
    ```
    python setup.py register -r pypitest # first upload only
    python setup.py sdist upload -r pypitest
    pip uninstall pyshtools
    pip install -i https://testpypi.python.org/pypi/pyshtools/X.X pyshtools
    ```
    If this works, you can do the same thing for pypi:
    ```
    python setup.py register -r pypi # first upload only
    python setup.py sdist upload -r pypi
    pip uninstall pyshtools
    pip install pyshtools
    ```
    After the package is updated, the pypi description should be edit directly on the webpage, to make sure that the relative links from the README.md are absolute links to github.
    
10. Update the homebrew installation by editing the file shtools.rb in the homebrew-shtools repo. First, change "url" to point to new version (the link can be found on the release page). Then, download the file the url points to, determine its SHA256 hash using "shasum -a 256 filename", and update the SHA256 hash. Finally,

    ```
    git add -u
    git commit
    git push
    ```

11. Update the documentation at shtools.oca.eu by copying the files index.html and www.

12. Update the version number for the **next** release in the develop branch in the files

    ```
    Makefile
    VERSION
    index.html
    pyshtools/__init__.py
    www/history.html
    ```

13. Change ISRELEASED to False in `setup.py` in the develop branch:

    ```
    git checkout develop
    # change ISRELEASED to False in setup.py
    git add -u
    git commit -m 'Change ISRELEASED to False'
    git push
    ```

14. Build wheels:

    ```
    git clone https://github.com/shtools/build-shtools.git
    cd build-shtools
    git submodule update --init
    cd shtools
    git checkout master
    cd ..
    git commit -a -m "Update SHTOOLS to master"
    git push
    ```
