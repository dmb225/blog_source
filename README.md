
# Workflow

This flow assumes you use Git Flow. Change the git commands accordingly if you are using another flow)

- Create a git branch: `git flow feature start <branch>`
- Create one or more new articles / E-edit previous articles: create the files as pelican/content/<slug>.markdown
- Commit: `git commit` (repeat 2 and 3 until you are satisfied with the results)
- Merge the branch: `git flow feature finish`
- Release: `./release.sh` (this runs Punch to create a new release)
- Deploy: `./deploy.sh` (this runs Pelican to create the static site and copies everything in the deploy directory)
- Publish: `./publish.sh` (this adds, commits, and pushes the files in the deploy directory)

Each one of these steps, with the notable exception of the second one, is performed through a single command and takes up to few seconds in the worst case.

If you want to have control on the publishing process, run the git commands manually in the deploy directory, but you can safely use the provided Make directive.