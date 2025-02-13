## v9?

- We have moved the root check to the beginning of the script, and improved DEBUG handling with two different modes. `DEBUG=0` is still for production, and `1` is still for the DEBUG we previously knew downloading to the directory it is running from, but `2` will download to temporary folder, will detect updates, but will not install anything, but it will notify the user (almost as running the script without root before).
- `pkgName` in a label can now be searched for. An example is logitechoptions, where only the name of the pkg is given, and not the exact file path to it.
- `LSMinimumSystemVersion` will now be honered, if the `Info.plist` in the app is specifying this. That means that an app that has this parameter in that file and it shows that the app requires a newer version of the OS than is currently installed, then we will not install it.
- New variable `RETURN_LABEL_NAME`. If given the value `1`, like `RETURN_LABEL_NAME=1` then Installomator only returns the name of the label. It makes for a better user friendly message for displaying in DEPNotify if that is integrated.
- Changed logic if `IGNORE_APP_STORE_APPS=yes`. Before this version a label like `microsoftonedrive` that was installed from App Store, and that we want to replace with the “ordinary” version, Installomator would still use `updateTool`, even though `IGNORE_APP_STORE_APPS=yes`. So we would have to have `INSTALL=force` in order to have the app replaced, as `updateTool` would be used. But now if `IGNORE_APP_STORE_APPS=yes` then `updateTool` will be not set, and the App Store app will be replaced. BUT if the installed software was not from App Store, then `updateTool` will not be used, and it would be a kind of a forced install (in the example of `microsoftonedrive`), except if the version is the same (where installation is skipped).
- Added variable `SYSTEMOWNER` that is used when copying files when installing. Default `0` is to change owner of the app to the current user on the Mac, like this user was installing this app themselves. When using `1` we will put “root:wheel” on the app, which can be useful for shared machines.

## v8.0

- removed leading `0` from the version because it has lost all meaning (thanks to @grahampugh for the inspiration)
- Installomator now detects when an app is already installed, and will display notifications correctly the user based on if the app was updated or installed for the first time.
- New variables for labels that should be installed using CLI: `CLIInstaller` and `CLIArguments`. When the installer app is named differently than the installed app, then the variable `installerTool` should be used to name the app that should be located in the DMG or zip. See the label __adobecreativeclouddesktop__ to see its use.
- `buildLabel.sh` has been improved to build GitHub software labels much easier. In essense if the URL contains github.com, then it will try to find if it's the latest version or if variable `archiveName` is needed for finding the software. Also improved messaging throughout the script, as well as handling a situation where a pkg does not include a “Distribution” file, but a “PackageInfo”.
- MDM script extended with `caffeinate` so Mac will not go to sleep during the time it takes installomator to run. Especially during setup, this can be useful.
- Microsoft labels with `updateTool` variable, is updated to run `msupdate --list` before running the updateTool directly. Problems have been reported that the update would fail if the `--list` parameter for the command was not run first. This should help with the Jamf agent stalling during installation.
- Added bunch of new labels, and improved others.

## v0.7

- default for `BLOCKING_PROCESS_ACTION`is now `BLOCKING_PROCESS_ACTION=tell_user` and not `prompt_user`. It will demand the user to quit the app to get it updated, and not present any option to skip it. In considering various use cases in different MDM solutions this is the best option going forward. Users usually choose to update, and is most often not bothered much with this information. If it's absoultely a bad time, then they can move the dialog box to the side, and click it when ready.
- script is now assembled from fragments. This helps avoid merging conflicts on git and allows the core team to work on the script logic while also accepting new labels. See the "Assemble Script ReadMe" for details.
- We now detect App Store installed apps, and we do not replace them automatically. An example is Slack that will loose all settings if it is suddenly changed from App Store version to the "web" version (they differ in the handling of settings files). If `INSTALL=force` then we will replace the App Store app. We log all this.
- Change in finding installed apps. We now look in /Applications and /Applications/Utilities first. If not found there, we use spotligt to find it. (We discovered a problem when a user has Parallels Windows installed with Microsoft Edge in it. Then Installomator wanted to update the app all the time, becaus spotlight found that Windows version of the app that Parallels created.)
- Added bunch of new labels, and improved others.
- Renamed `buildCaseStatement.sh` to `buildLabel.sh` and improved it a lot. It is a great start when figuring out how to create a new label for an app, or a piece of software. Look at the tutorials in our wiki.
- Mosyle changed their app name from Business to Self-Service

## v0.6 - 2021-07-14

- several new and updated labels, for a total of 302
- versionKey variable can be used to choose which Info.plist key to get the version from
- an appCustomVersion() {} function can now be used in a label
- with INSTALL=force, the script will not be using updateTool, but will reinstall instead
- added quit and quit_kill options to NOTIFY
- updated buildCaseStatement.sh
- updated buildInstallomatorPkg.sh to use notarytool (requires Xcode 13)
- several minor fixes

## v0.5 - 2021-04-13

- Major update and now with help from @Theile and @Isaac
- Added additional `BLOCKING_PROCESS_ACTION` handlings
- Added additional `NOTIFY=all`. Usuful if used in Self Service, as the user will be notified before download, before install as well as when it is done.
- Added variable `LOGO` for icons i dialogs, use `LOGO=appstore` (or `jamf` or `mosyleb` or `mosylem` or `addigy`). It's also possible to set it to a direct path to a specific icon. Default is `appstore`.
- Added variable `INSTALL` that can be set to `INSTALL=force` if software needs to be installed even though latest version is already installed (it will be a reinstall).
- Version control now included. The variable `appNewVersion` in a label can be used to tell what the latest version from the web is. If this is not given, version checking is done after download.
- For a label that only installs a pkg without an app in it, a variable `packageID` can be used for version checking.
- Labels now sorted alphabetically, except for the Microsoft ones (that are at the end of the list). A bunch of new labels added, and lots of them have either been changed or improved (with `appNewVersion` og `packageID`).
- If an app is asked to be closed down, it will now be opened again after the update.
- If your MDM cannot call a script with parameters, the label can be set in the top of the script.
- If your MDM is not Jamf Pro, and you need the script to be installed locally on your managed machines, then take a look at [Theiles fork](https://github.com/Theile/Installomator/). This fork can be called from the MDM using a small script.
- Script `buildCaseStatement.sh` to help with creating labels have been improved.
- Fixed a bug in a variable name that prevented updateTool to be used
- added `type` variable for value `"updateronly"` if the label should only run an updater tool.


## v0.4 - 2020-10-19

- you can now set script variables as an argument in the form `VARIABLE=value`. More detail on this in the README file, 'Configuration from Arguments.' (#26, #50, #72, and #73)
- change `downloadFromGit` to match file types better (#58)
- implemented a workaround for changed behavior of `xpath` in Big Sur (#80)
- added an option `prompt_user_then_kill` to `BLOCKING_PROCESS_ACTION` which will kill the process after the third unsuccessful attempt to quit (#78, thanks Patrick Atoon @raptor399)
- added several new labels for total of 116


## v0.3 - 2020-07-23

- added several new labels for total of 98
- removed the powershell labels, since the installer is not notarized
- when run without any arguments, the script now lists all labels
- changed how zips are expanded because this was broken on Mojave
- improved logging in some statements
- several more minor improvements


## v0.2 - 2020-06-09

- many fixes for broken URLs and other bugs
- the `pkgInDmg` and `pkgInZip` now search for a pkg file in the archive in case the file name varies with the version
- notification on successful installation can be suppressed with the `NOTIFY` variable
- Apple signed installers and apps that don’t have a Team ID are verified correctly now
improved logging
- several new applications: count increased from 62 in 0.1 to 87 in 0.2


## v0.1 - 2020-05-12
