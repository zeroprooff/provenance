# .changelog directory information

This `.changelog/` directory is primarily used to coordinate unreleased changes and
ultimately to generate the release notes and changelog content for a release.

<!-- TOC -->
  - [Overview](#overview)
  - [Adding Changes](#adding-changes)
    - [Using add-change.sh](#using-add-changesh)
    - [Using unclog](#using-unclog)
  - [Dependencies](#dependencies)
  - [Section Names](#section-names)
  - [Unclog](#unclog)
  - [Entry Files](#entry-files)
  - [Releases](#releases)
  - [Finding Changes](#finding-changes)



## Overview

As changes are made in this repo, entries should be added under the `.changelog/unreleased/` directory.
By storing entries in different files for different changes, we remove a common source of merge conflicts.

We use [unclog](https://github.com/informalsystems/unclog) to help create and manage these changelog entries.
However, we don't use it to create the entire content of the `CHANGELOG.md` file, just new content.

The `CHANGELOG.md` is usually only updated as part of the release process.



## Adding Changes

When a change is being made to this repo, at least one changelog entry should be created about it.
The easiest way to do this is using `add-change.sh`, but [unclog](https://github.com/informalsystems/unclog) can also be used.


### Using add-change.sh

Usage:
```plaintext
Usage: add-change.sh [<num>] [<id>] [<section>] [<message>]

Each argument can alternatively be provided using flags:
    [-n|--issue|--issue-no|-p|--pr|--pull-request] <num>
    [-i|--id] <id>
    [-s|--section] <section>
    [-m|--message] <message>
```

See `add-change.sh --help` for full usage details.
In general, though, it tries to be as natural as possible to use.
The `<num>` can be omitted if your branch name has the format `<user>/<num>-...`.
The `<id>` can be omitted to just use the branch name (without the `<num>-` part if it has it).
The `<section>` is a fuzzy-match field against the list of possible sections.
E.g. you can provide "bug" and it'll convert that to "bug-fixes".
If not provided (or there's confusion), you'll be prompted to select one using `fzf` (or get an error if `fzf` isn't available).
If the `<message>` isn't provided, behavior depends on the `<section>`.
If the section is "dependencies", then the `get-dep-changes.sh` script will be executed to create the message content.
If it's any other section, the file will be created with a `TODO` entry.


Example for changes that do **not** have a related GitHub issue:

```console
$ .changelog/add-change.sh 123 fix-the-thing bug 'Fix the thing that was broken'
```

If your branch is named `yourname/fix-the-thing`, then this next command is equivalent to the previous one:

```console
$ .changelog/add-change.sh 123 bug 'Fix the thing that was broken'
```

Example for changes that **do** have a related GitHub issue.

```console
$ .changelog/add-change.sh --issue 123 fix-the-thing bug 'Fix the thing that was broken'
```

If your branch is named `yourname/123-fix-the-thing`, then this next command is equivalent to the previous one:

```console
$ .changelog/add-change.sh bug 'Fix the thing that was broken'
```

All of these example commands will create the file `.changelog/unreleased/bug-fixes/123-fix-the-thing.md`
with this content (respectively):

```md
* Fix the thing that was broken [PR 123](https://github.com/provenance-io/provenance/pull/123).
```

or

```md
* Fix the thing that was broken [#123](https://github.com/provenance-io/provenance/issues/123).
```


### Using unclog

Example for changes that do **not** have a related GitHub issue:

```console
$ unclog add --pull-request 123 --section bug-fixes --id fix-the-thing --message 'Fix the thing that was broken'
```

Example for changes that **do** have a related GitHub issue.

```console
$ unclog add --issue-no 123 --section bug-fixes --id fix-the-thing --message 'Fix the thing that was broken'
```

Both of those example commands will create the file `.changelog/unreleased/bug-fixes/123-fix-the-thing.md`
with this content (respectively):

```md
* Fix the thing that was broken [PR 123](https://github.com/provenance-io/provenance/pull/123).
```

or

```md
* Fix the thing that was broken [#123](https://github.com/provenance-io/provenance/issues/123).
```

Each entry file can have as many bullet-points as needed, and bullet-points can span multiple lines, just like regular markdown.

If the change you are making should have multiple entries in different sections, make an entry file in each applicable section.

Entry files can also be created manually, but should conform to the following standards:

* The file should be named `<num>-<id>.md` and placed in a valid `<section>` directory in `unreleased`.
* The first line of the file should have the format `* <message> <link>.`.
* The file should not have any blank lines.
* The file should use standard markdown.
* Imagine bullet-points both above and below this entry. It all should form a single unbroken unordered list.



## Dependencies

When making changes to `go.mod`, you should use the `.changelog/get-dep-changes.sh` script to create the dependencies changelog entry.

```console
$ go mod tidy
$ .changelog/get-dep-changes.sh --pull-request 123 --id bump-the-thing
```

That will create the file `.changelog/unreleased/dependencies/123-bump-the-stuff` with content generated by analyzing the changes made to `go.mod`.

Here too, if there is an issue number for the change, you should use the `--issue-no` (or `-n`) flag instead of `--pull-request` (or `-p) in order to have the correct links.



## Section Names

The valid `--section` values (for `unclog`) are defined in the comment at the top of the `CHANGELOG.md` file.
The quoted stanza strings are lower-cased and spaces changed to dashes to get the valid section names.
That list also defines the order that the sections will be in for each version.

The `.changelog/get-valid-sections.sh` script will output the exact list of valid sections.

```console
$ .changelog/get-valid-sections.sh
features
improvements
bug-fixes
deprecated
client-breaking
api-breaking
state-machine-breaking
dependencies
```

You can also get them using the `make get-valid-sections` target.

All directories in `.changelog/unreleased/` must be one of those or else linting will fail.



## Unclog

We use [unclog](https://github.com/informalsystems/unclog) primarily to create new entry files that will later be collected into some release notes.
We don't use much else of that system though. E.g. our `CHANGELOG.md` file is **not** simply the output of `unclog build`.

Some `unclog` commands require that the `EDITOR` environment variable has been set, e.g.:

```console
$ export EDITOR="$( which vim )"
```

To create our release notes (in `.changelog/prep-release.sh`), we start with the output of `unclog build --unreleased` and then clean it up and reorder things.

You might also want to use the `unclog build [--unreleased|--all]` command to view the content under `.changelog/`.
However, if a `summary.md` file is missing, or is just the default comment, `unclog` will open your editor for it.
If it does, it will end up writing one or more `summary.md` files.
So, if you do this, make sure you don't accidentally check in unwanted changes to the `summary.md` file(s).

We don't use the `unclog release` command because that will _always_ try to open the editor to make a change to (or create) the `summary.md` as it's moved to the version directory.
But we need to have `.changelog/unreleased/summary.md` written before we're ready to move that content.



## Entry Files

The `unclog add` command will create files with the path `.changelog/unreleased/<section>/<num>-<id>.md`.
It's very important to make sure these files are correct in the PR that creates them.
If at all possible, the file should not be touched once the PR has been merged (until it is deleted as being part of a release).
It's easier for git to keep track of it (without complaining) when the content and filename remain unchanged.
Further, we usually backport PRs by using `git cherry-pick` on a squash-and-merge coming from a PR.
If the entry for that change is update by a later PR, there's a very good chance the release branch will not get the followup.

Standard lifecycle of an entry file that is part of a release candidate:

1. File is created and included in a PR targeting `main`.
2. The PR is merged into `main`.
3. The file is backported to the `.x` branch (or included in the initial creation of that branch).
4. In the `.x` branch, the file is moved to the rc version dir (from `unreleased/`) as part of the PR to mark the new release candidate version.
5. In `main`, the file is deleted as part of a PR to mark the new release candidate version there too.
6. In the `.x` branch, the file is moved to full version dir (from the rc version dir) as part of the PR to mark the new full version.

If the entry isn't part of a release candidate, then in step 4 it gets moved to the full version directory, and step 6 doesn't happen.



## Releases

When preparing to mark a release, you should use the `.changelog/prep-release.sh` script.

That script will:
1. Create or Update the `RELEASE_CHANGELOG.md` file.
2. Add the new version to the `CHANGELOG.md` file.
3. Create a new version directory in the `.changelog/` folder with content from `unreleased/` and any rcs for this version.

The results of the `.changelog/prep-release.sh` script are to be applied to the `release/vA.B.x` branch.
When marking the release in `main`, do not include the new version directory, but do delete the applicable entries from `.changelog/unreleased/`.
That is, the `.changelog/<version>` directories should only ever exist on the `.x` branch.
This is primarily to reduce confusion if there is a discrepancy between the `CHANGELOG.md` content and an entry file's content.
It also helps keep things tidy and file counts lower.

If you need to make tweaks or clarifications to the content, you should make the changes in the `RELEASE_CHANGELOG.md` file first, then copy/paste those into the `CHANGELOG.md` file.
You should NOT update the changelog entry files, though (other than moving them).

And to reiterate, the `RELEASE_CHANGELOG.md` file and `.changelog/<version>` directories should never exist on `main`, only in the `.x` branch.

If you can't, or don't want to use the `.changelog/prep-release.sh` script, here's how to do things manually.

To manually create the new `RELEASE_CHANGELOG.md` file:

1. If this is a full version and there were release candidates, move all the rc content into unreleased.
2. Run `unclog build --unreleased` to get the preliminary content.
3. Change the version header into a link and include the release date.
4. Change the section header casing to title case (from all upper-case).
5. Reorder the sections to match how they're listed in the top `CHANGELOG.md` comment.
6. Reorder the `Dependencies` section so that entries for the same library are next to each other.
7. Update all the link text to be `[PR <num>]` for prs, and `[#<num>]` for issues determined by the link path. The `<num>` is extracted from the link too to ensure that the text matches the target.
8. If a link has a period both before (ignoring whitespace) and after it, remove the one before it.
9. Add the "Full Commit Diff" link(s).
10. If this is a release candidate of 2 or more, add the new release notes to the top of the existing one, and put a divider `---` between them. That way all rcs for the given version are in the release notes.
11. If this is a full release, or a release candidate of 1, overwrite any existing release notes with the new content.

To manually update the `CHANGELOG.md` file:

1. Generate the new release notes.
2. Copy the new content for only this release from the release notes. I.e. if this is rc2, the release notes will also have rc1, we only want rc2 copied here since rc1 should already be listed in the `CHANGELOG.md` file.
3. In `CHANGELOG.md`, Add a new divider directly above the first one (between the `## Unreleased` section, and the first version section).
4. Past the new content between those two dividers, with an empty line before and after each divider.
5. Remove the blurb between the `##` version header, and the first `###` section header.

To manually update the `.changelog/` entries:

1. Move the `.changelog/unreleased` directory to `.changelog/<version>`, e.g. `mv .changelog/unreleased .changelog/v1.13.0`.
2. Delete the old `.gitkeep` file, e.g. `rm .changelog/v1.13.0/.gitkeep`.
3. Create a new `.changelog/unreleased` directory, e.g. `mkdir .changelog/unreleased`.
4. Create the new `.gitkeep` file, e.g. `touch .changelog/unreleased/.gitkeep`.



## Finding Changes

The content in `CHANGELOG.md` should be trusted over the content in the `.changelog/` directory.
The `CHANGELOG.md` file will contain all the info on all releases (either full or release candidates).
On `main`, the `.changelog/` directory will only contain information about unreleased changes.
On a `.x` branch, the `.changelog/` folder will contain information on all releases for this minor version as well as stuff that is ready to be included in the next patch release.

As part of the release process, it will be common to update the `CHANGELOG.md` file using the release prep script, but then make tweaks or clarifications to it.
Those tweaks and clarifications probably won't be reflected in the entry files though.
That is why the `CHANGELOG.md` file is the source of truth.

One way to find information about unreleased changes is to use `unclog build --unreleased`.
That will combine all info in `.changelog/unreleased/` and output an unofficial release-notes to stdout with the unreleased changes.
