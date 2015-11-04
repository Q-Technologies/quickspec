# Quickspec

Quickly generate an RPM SPEC file for Web applications.

## Purpose

Sometimes the overhead of defining a SPEC file is not justfified when you just want to deploy some files that comprise a Web application - especially when you have a number of web applications you are maintaining.  There are other ways to deploy files, but the RPM format solves a number of problems (preserving config files, removing redunant files, running pre/post scripts, ignoring logs, ability to roll back to previous version easily, etc).  This script can obviously be used to deploy any app, by there will be a point were it it is more efficient to create the SPEC file manually.

## Installation

Just install the script (`quickspec`) somewhere in your `PATH`, e.g. `/usr/local/bin`.

## Usage

### Create a configuration file

The configuration file is in YAML format.  The file extension does not matter, but you could use `.quickspec` to identify it's purpose.  Many of the fields specified in a SPEC file need to be specified in the qiuckspec file:

Required
* **Name** - the name of the package
* **Version** - the version of the code
* **Release** - the version of the RPM
* **License** - the license the application is released under
* **Url** - The URL pointing to where the source can be found
* **Summary** - a short summary of the application
* **Group** - which RPM group to add it to
* **Author** - name or email address of the package author
* **SrcDir** - the top of the source code directory
* **PkgDir** - the directory containing files that determine how the app is managed by the system (e.g. start/stop scripts, crontab, etc)
* **InstallRoot** - where to install the main application files
* **BuildArch** - destination CPU architecture (usually noarch for interpreted scripts)
* **BuildRoot** - Where to build the RPM (i.e. the tempory working files)

Optional
* **IncFiles** - a regular expression determining which files to include from the source directory.  Default is all files.
* **ExcFiles** - a regular expression specifying which files to exclude from the source directory (takes precedence over include).  Default is not to exclude.
* **Description** - a description of the package.  Default is the summary.
* **ChangeLog** - a source code changelog. Omitted if not specified.
* **RunAs** - Default is root and root's home.
  * **username** - the username to run the application as
  * **home** - the home directory of the runas user
* **RCFile** - the name of the start/stop init script.  Omitted if not specified.
* **CronFile** - the name of the crontab file - will be installed in `/etc/cron.d`.  Omitted if not specified.
* **LogDir** - the location of the application logs - it will be owned by the RunAs user.  Omitted if not specified.

### Invoking

To create the SPEC file, run:

    quickspec /path/to/config/file.quickspec

This will create a SPEC file in: **PkgDir**

Additionally, you can specify a number of systems on the command line that will be used to actually build the RPM package.  The login account being used to run this script mush have an SSH key authenticated connection (i.e. passwordless) and the rpmbuild tools need to be installed on the build systems.  For example:


    quickspec /path/to/config/file.quickspec rpmbuildhost1 rpmbuildhost2 rpmbuildhost3

will create the SPEC file, then SSH into each of the specified RPM build hosts and create the RPMs.  This is handy if you need to create an RPM across multiple distributions and major versions (e.g. RedHat 6, Centos 7, SLES 12).  Each of the created RPMs will be copied back to **PkgDir**/RPMs.

### Error Checking

At this stage there is very little error checking, but all the output from the raw RPM commands are displayed on the console, so it should be relatively straigh forward to track down any errors.


