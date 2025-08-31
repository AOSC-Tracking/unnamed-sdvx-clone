# Tracking repository for unnamed-sdvx-clone

We have to track patches for unnamed-sdvx-clone here because the upstream repo uses submodules.

## Standard Operation Procedure

1. Fetch the desired tag from unnamed-sdvx-clone (e.g. `git fetch https://github.com/Drewol/unnamed-sdvx-clone.git v0.6.0`).
2. Switch to `FETCH_HEAD` (`git checkout FETCH_HEAD`).
3. Recursively update submodules (`git submodule update --init --recursive`).
4. Recursively remove `.gitattributes` files, `.gitignore` files, `.gitmodules` files and `.git` directories except the one in the top-level (`find . \( -name .gitattributes -o -name .gitignore -o -name .gitmodules \) -delete; find . -name .git`, note that `.git` directories have to be manually removed).
5. Remove the subproject attribute of submodule folders from git (`git submodule | awk '{print $2}' | xargs git rm --cached`).
6. Update the index using all files in the entire working tree (`git add -A`).
7. Make a soft reset to the tarball branch (`git reset --soft tarball`).
8. Redo step 6, then create a new commit with message of `chore: import v$VER` (`git commit -m "chore: import v$VER"`).
9. Create a lightweight tag with name of `v$VER` (`git tag "v$VER"`).
10. Push the `tarball` branch and the tag just created to the remote (`git push; git push --tags`).
11. Create a new branch with name of `aosc/v$VER`, pointing to the current `HEAD`, then switch to it (`git checkout -b "aosc/v$VER"`).
12. Apply all needed commits and/or patches.
13. Create a lightweight tag with name of `aosc/v$PKGEPOCH%$PKGVER-$PKGREL` (`git tag "aosc/v$PKGEPOCH%$PKGVER-$PKGREL"`). If any of these variables equals zero, omit the related section and the corresponding separator. For the definition of these variables, refer to [autobuild4](https://github.com/AOSC-Dev/autobuild4).
14. Push the `aosc/v$VER` branch and the tag just created to the remote (`git -c "push.autoSetupRemote=true" push; git push --tags`).
15. Prepare patches for the ABBS tree (`git format-patch "tags/v$VER..tags/aosc/v$PKGEPOCH%$PKGVER-$PKGREL"`).
