OBS package from Git
====================

This tool automates making an openSUSE Build Service package from a Git
repository. It is used for [Snapper][s] and [Libstorage][l].

[s]: https://github.com/openSUSE/snapper
[l]: https://github.com/openSUSE/libstorage

You see, sometimes it is enough to simply tar up the Git repo with
`git archive` or a similar tool. We can do that for YaST since its build
scripts are ready for that. But more often the distribution tarball is
expected to contain a `configure` script which you do not want to commit to
Git.

Naturally there is a [standard way][std], which is to run `make dist`.
But this requires that you have the development dependencies installed.
Why on Earth would you not have them installed? Because you are a colleague of
the regular maintainer who just needs to fix one line. Or because you are a
Continuous Integration server, which did have the dependencies right
initially, but Progress, so now you have an outdated set.

[std]: https://www.gnu.org/prep/standards/html_node/Standard-Targets.html

Therefere this tool exists to bootstrap the dependencies by an `osc build`
run.

Example
-------

This is copied from a Makefile in Snapper:

```sh
.PHONY: package
package: obs-package-from-git
	./$^ \
		-P YaST:Head -p snapper \
		-o .obsdir \
		-c 'make -f Makefile.repo && make package'

# A script that is shared with other projects and should be always refreshed
URL=https://raw.githubusercontent.com/openSUSE/obs-package-from-git/master/obs-package-from-git
.PHONY: always_out_of_date
obs-package-from-git: always_out_of_date
	wget -O $@ $(URL) && chmod +x $@
```
