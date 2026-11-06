---
name: changelog
description: Generate or update natural-language changelogs from Git, saved by version in changelogs/ and linked from CHANGELOG.md. Always use when asked to create a changelog for a version or branch.
---

# Changelog

Generate a useful summary for people who use the project, grounded in repository evidence. The expected branch is `release/*`; derive the version from its name or, as a fallback, from the root `VERSION` file on that branch.

## Resolve the branch and version

1. Identify the repository root and inspect the working tree status. Use the requested branch; if only a version is provided, look for its `release/<version>` branch with or without a `v` prefix. If neither is specified, inspect the current branch. Resolve existing local or remote references without switching branches. Ask if multiple candidates exist and the intended one is unclear.
2. The branch must match `release/*`. Extract an unambiguous version from its suffix, such as `release/0.2.0` or `release/v0.2.0`. Allow prerelease and build metadata; do not turn names such as `release/next` into invented versions.
3. If the branch name does not provide an unambiguous version, read `VERSION` from the root of that same Git reference's tree, not from another branch or uncommitted local changes. It must contain a single nonempty version suitable for use as a filename. Do not substitute other manifests for this source.
4. **Stop before writing** if the branch does not exist, does not match `release/*`, there is no current branch because HEAD is detached, or neither the branch name nor `VERSION` resolves the version. Ask which branch to use or whether the user wants to use the current one, and warn: “The changelog may be inaccurate if it is not generated from the corresponding release branch.” Explain the specific problem and also ask for the version if missing. Do not treat silence as confirmation.
5. If the user has already explicitly authorized a branch outside `release/*`, respect that choice without asking again; warn about possible inaccuracies and resolve the version from `VERSION` or their explicit instruction. Do not invent a version to proceed. If the requested version conflicts with the resolved one, ask for clarification before writing.

Normalize only the prefix: use a single `v` in the title and filename (`v0.2.0`). Preserve the rest of the version, including prerelease and build metadata; reject paths, directory separators, or multiline content.

## Determine the release changes

- Fix the target reference and always compare against it, even when it is not the current branch. Exclude uncommitted local changes.
- Use the base reference supplied by the user, if any. Otherwise, identify the previous published version's tag that is an ancestor of the target, using the project's tags and release history. Exclude the target version's own tag and verify ancestry; do not simply select the most recently dated tag across the repository.
- Inspect both the commits in `base..target` and the diff between the endpoints. Read relevant files to understand the actual effects, especially when commit messages are vague. Avoid duplicate entries caused by merges or cherry-picks.
- If there is no previous tag, look for the commit corresponding to the previously documented version. For a confirmed first release, the entire history up to the target may be used. If the boundary remains ambiguous, ask for the base reference before generating: a merge-base with the main branch may exclude release changes already integrated and must not be assumed to be a substitute.
- Do not perform checkout, reset, stash, commits, tags, push, or publication to generate these documents. If local history is insufficient, explain which reference is missing and obtain the necessary context before attributing changes.

## Write the changelog

Write concise natural-language entries describing added features, behavior changes, and bug fixes. Synthesize related commits into user-facing changes. Avoid file lists and internal details without a relevant effect on the reader. Do not claim performance, security, or compatibility improvements without evidence.

**Do not include commits or branches in changelog documents or the index.** This includes commit hashes, commit links, copied commit messages, commit lists, branch names, branch links, and Git comparison ranges. Use Git history only as evidence for writing the natural-language summary. Keep any report about the source branch or comparison range in the conversational response, outside the generated Markdown files.

Use the language of existing changelogs unless the user requests another; without a precedent, use the language of their request. Keep the headings `Added`, `Changed`, and `Fixed` as shown in the requested format. Omit empty sections; if there are no verifiable changes, say so without inventing entries.

The heading must be `# v<version> - YYYY-MM-DD`. Use the publication date if documented; otherwise, use the current date in the user's time zone. When updating an existing changelog, preserve its date unless instructed otherwise or evidence shows it needs correction.

Example format:

```markdown
# v0.2.0 - 2026-11-06

## Added
- Support for encrypted uploads.
- Upload preferences dialog.

## Changed
- Improved chunk upload performance.

## Fixed
- Fixed an issue with large file uploads.
```

## Save and maintain the index

1. Create `changelogs/` at the repository root if needed. Save the document as `changelogs/v<version>.md`, for example `changelogs/v0.2.0.md`.
2. If a changelog already exists for the same version, update it without duplicate entries and preserve manual annotations that remain valid and comply with the changelog content rules. If its filename omits `v`, reuse it to avoid two documents for the same version.
3. Always create or update the root `CHANGELOG.md` as an index of all version changelogs in `changelogs/`, using relative links. Preserve valid introductory content and avoid duplicate or broken links.
4. Sort the index from newest to oldest by each changelog's date. For date ties, use version order when comparable: higher versions first and a final version before its prerelease. Do not use alphabetical order or file modification dates. If missing information prevents correct ordering, ask rather than invent it.

Example index:

```markdown
# Changelog

- [v0.2.0 - 2026-11-06](changelogs/v0.2.0.md)
- [v0.1.0 - 2026-10-01](changelogs/v0.1.0.md)
```

Before finishing, verify that the version, date, path, and links agree; the index contains every changelog exactly once and in order; entries are supported by the selected comparison; and the generated Markdown contains no commit or branch references. Report the generated files, source branch, comparison range, and any limitations affecting accuracy in the conversational response only.
