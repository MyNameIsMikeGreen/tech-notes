Updating Javascript Dependencies with NPM
=====

# Why?
GitHub is shouting at us :(

# What to do

* `npm install -g npm`

* Keep running these until something seems to happen:
  * `npm audit fix`
  * `npm audit fix --force`

* Go through `package-lock.json` and manually update any transitive dependencies that were not automatically updated.

* If dependencies updates to a version not installed on a machine that will run the code (e.g. the Jenkins box), install it.

* Repeat until you give up.
