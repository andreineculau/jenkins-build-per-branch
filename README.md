# jenkins-build-per-branch

This is a fork of https://github.com/entagen/jenkins-build-per-branch .

It allows you to have a template job configured for one branch, and automatically check for new branches in a git repository and create jobs, based on the template, pointing to the newly discovered branches.

## Requirements

* [Jenkins Git Plugin][https://wiki.jenkins-ci.org/display/JENKINS/Git+Plugin]
* [Jenkins Gradle plugin][http://wiki.jenkins-ci.org/display/JENKINS/Gradle+Plugin]
* [Nested View Plugin][https://wiki.jenkins-ci.org/display/JENKINS/Nested+View+Plugin] (optional, see [original docs](README.original.md) for info)

## Installation

Imagine you have already a job suffixed with the branch name/placeholder, like this:

* `testing-my-repo-master`
* or `testing-my-repo-origin_master` (any separator works)
* or `testing_my_repo-Xbranch` (Xbranch isn't a real branch, and most certainly this job is disabled)

Create a freestyle project

* called e.g. `testing-my-repo-Xbranch-sync-with-repo`
* under **Source Code Management**, pick Git
* point to this `jenkins-build-per-branch` URL and the latest tag (it's not safe to use branch names!)
* under **Build Triggers**, you could go for **Build periodically** and `@midnight`
* under **Build**, add a `Invoke Gradle script`
* I currently use Gradle 2.4
* under **Tasks**, write `syncWithRepo`
* under **Switches** add one per line, in this format `-D<name>=<value>`

All available **Switches** can be found in the source-code [here](src/main/groovy/com/entagen/jenkins/Main.groovy#L9).

I currently use something like this

```
-DnoDelete=true
-DnoViews=true
-DjenkinsUrl=https://example.com/jenkins
-DallowSelfsignedSslCerts=true
-DgitUrl=git@example.com:repo.git
-DtemplateJobPrefix=testing-my-repo-
-DtemplateBranchName=Xbranch
-DbranchNameRegex=master|v.*
-DenableOnCreate=true
```

**NOTE**: `testing-my-repo-Xbranch-sync-with-repo` is disabled, but has cronjob builder/poller, so that the new jobs based on it, will be enabled and will start on their own.

**NOTE**: if you use this for branches that eventually get deleted, and you would like `jenkins-build-per-branch` to delete jobs for those pruned branches set `noDelete=false` AND MAKE SURE TO RENAME `testing-my-repo-Xbranch-sync-with-repo` to e.g. `sync-with-repo-testing-my-repo-Xbranch`, or else it will think `Xbranch-sync-with-repo` is a branch that doesn't exist anymore.

## Original docs

[README.original.md](README.original.md)

## License

[Apache 2.0](LICENSE)
