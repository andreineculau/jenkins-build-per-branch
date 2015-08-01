# jenkins-build-per-branch

This is a fork of https://github.com/entagen/jenkins-build-per-branch .

It allows you to have a template job configured for one branch, and automatically check for new branches in a git repository and create jobs, based on the template, pointing to the newly discovered branches.

## Requirements

* [Jenkins Git Plugin][https://wiki.jenkins-ci.org/display/JENKINS/Git+Plugin]
* [Jenkins Gradle plugin][http://wiki.jenkins-ci.org/display/JENKINS/Gradle+Plugin]
* [Nested View Plugin][https://wiki.jenkins-ci.org/display/JENKINS/Nested+View+Plugin] (optional, see [original docs](README.original.md) for info)

Before moving on, go to **Manage Jenkins > Configure System** and add a Gradle installation. I currently use Gradle 2.5.

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
* select Gradle version, so it will be installed automatically
* under **Tasks**, write `syncWithRepo`
* under **Switches** add one per line, in this format `-D<name>=<value>`

All available **Switches** can be found in the source-code [here](src/main/groovy/com/entagen/jenkins/Main.groovy#L9).

I currently use something like this

```
-DjenkinsUrl=https://example.com/jenkins
-DallowSelfsignedSslCerts=true
-DgitUrl=git@example.com:repo.git
-DtemplateJobPrefix=testing-my-repo-
-DtemplateBranchName=Xbranch
-DbranchNameRegex=master|v.*
-DenableJobRegex=.*
```

**NOTE**: `testing-my-repo-Xbranch-sync-with-repo` is disabled, but has cronjob builder/poller, so that the new jobs based on it, will be enabled at once and will start on their own.

## Change log

### 2015-08-01 - v2.0

* remove `noDelete`, `noViews` in favour of `deleteOld`, `views` - no double negations

### 2015-08-01 - v1.0

* based on upstream [547230eec39135a548b5ffacf8ee95e6577c56e3](https://github.com/entagen/jenkins-build-per-branch/commit/547230eec39135a548b5ffacf8ee95e6577c56e3)
* change `startOnCreate` to take a non-Boolean value to allow `buildWithParameters`, pass a token or a cause [PR](https://github.com/entagen/jenkins-build-per-branch/pull/34)
* less assumptions in the regex
* replace branch occurences, not job occurences
* new `allowSelfsignedSslCerts` - add support for self signed certificates [PR](https://github.com/entagen/jenkins-build-per-branch/pull/40)
* new `enable-job-regex`, `disable-job-regex` - enable or disable jobs based on regex [PR](https://github.com/entagen/jenkins-build-per-branch/pull/65)

## Original docs

[README.original.md](README.original.md)

## License

[Apache 2.0](LICENSE)
