<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [Merge 2.4 branch](#merge-24)
- [Change version in spark_parser/version.py. Then:](#change-version-in-sparkparserversionpy-then)
- [Update ChangeLog:](#update-changelog)
- [Update NEWS from ChangeLog. Then:](#update-news-from-changelog-then)
- [Make sure pyenv is running and check newer versions](#make-sure-pyenv-is-running-and-check-newer-versions)
- [Switch to python-2.4, sync that up and build that first since it creates a tarball which we don't want.](#switch-to-python-24-sync-that-up-and-build-that-first-since-it-creates-a-tarball-which-we-dont-want)
- [Update NEWS from master branch](#update-news-from-master-branch)
- [Check against all versions](#check-against-all-versions)
- [Make packages and tag](#make-packages-and-tag)
- [Upload single package and look at Rst Formating](#upload-single-package-and-look-at-rst-formating)
- [Upload rest of versions](#upload-rest-of-versions)
- [Push tags:](#push-tags)
- [Check on a VM](#check-on-a-vm)

<!-- markdown-toc end -->
# Merge 2.4 branch

    $ git checkout python-2.4
	$ pyenv local 2.4.6
	$ git merge

Resolve conflicts

	$ make check
    $ git commit
	$ git push
	$ git checkout master
	$ pyenv local 2.7.14

# Change version in spark_parser/version.py. Then:

	$ emacs spark_parser/version.py
    $ source spark_parser/version.py
    $ echo $VERSION
    $ git commit -m"Get ready for release $VERSION" .

# Update ChangeLog:

    $ make ChangeLog

#  Update NEWS from ChangeLog. Then:

	$ emacs NEWS
    $ make check
    $ git commit --amend .
    $ git push   # get CI testing going early

# Make sure pyenv is running and check newer versions

    $ pyenv local && source admin-tools/check-newer-versions.sh

# Switch to python-2.4, sync that up and build that first since it creates a tarball which we don't want.

    $ source admin-tools/setup-python-2.4.sh
    $ rm ChangeLog

    $ git merge master

# Update NEWS from master branch

    $ git commit -m"Get ready for release $VERSION" .

# Check against all versions

    $ source admin-tools/check-older-versions.sh
    $ source admin-tools/check-newer-versions.sh

# Make packages and tag

    $ admin-tools/make-dist-older.sh
    $ git tag release-python-2.4-$VERSION

    $ admin-tools/make-dist-newer.sh
    $ git tag release-$VERSION

# Upload single package and look at Rst Formating

    $ twine upload dist/spark_parser-${VERSION}-py3.3.egg

# Upload rest of versions

    $ twine upload dist/spark_parser-${VERSION}*

# Push tags:

    $ git push --tags

# Check on a VM

    $ cd /virtual/vagrant/virtual/vagrant/ubuntu-zesty
	$ vagrant up
	$ vagrant ssh
	$ pyenv local 3.5.2
	$ pip install --upgrade spark_parser
	$ exit
	$ vagrant halt
