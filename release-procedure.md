SHTOOLS Release Procedure
-------------------------

1. Start by making all changes in the develop branch
```
git checkout develop```

2. Update the version number in main Makefile, update the file "VERSION”, and update the www/history.html.

3. Update the documentation: 
```
make doc
```
4. Add and commit new files to develop: 
```
git status
git add -A
git commit
git push
```
5. Merge develop into master and tag as new version:
```
git checkout master
git merge develop
git push
git tag -s v3.2 -m "Version 3.2"
git push origin v3.2
``` 
6. Update gh-pages
```
git checkout gh-pages
git checkout master index.html
git checkout master www
git add -A
git commit -m "Update web documentation for v3.2"
git push
```
7. Create a github release. Go to https://github.com/SHTOOLS/SHTOOLS/releases and draft a new release. After this is done, a zipped archive will be sent to Zenodo, which will create a doi for citation. With this citation, you can then update the github release notes. Furthermore, this will send a zipped archive to the project page at Sourceforge.

8. Update the homebrew installation by editing the file shtools.rb in the homebrew-shtools repo. First, change "url" to point to new version (the link can be found on the release page). Then, download the file the url points to, determine its SHA256 hash using "shasum -a 256 filename", and update the SHA256 hash. Finally, 
```
git add -A
git commit
git push
```
9. Update the documentation at shtools.ipgp.fr by copying the files index.html and www.
