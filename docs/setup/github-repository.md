# GitHub Repository Setup

Follow these steps to set up new GitHub repositories and their pull requests.

## Create new repository

- [ ] Give the repository a proper name.
    - Is a certain prefix required?
    - Should a certain word be included?
- [ ] Add a repository description and website.
- [ ] Use `main` as the default branch.
- [ ] Disable unused features:
    - Wikis
    - Issues
    - Projects
- [ ] Configure team access.
- [ ] Protect the `main` branch:
    - Require a pull request before merging.
    - Require at least 1 approval.
    - Dismiss stale pull request approvals when new commits are pushed.
    - Require conversation resolution before merging.
    - Do not allow bypassing the above settings.
- [ ] Create GitHub environments:
    - Development
    - Staging
    - Production
- [ ] Add Actions repository secrets, e.g. Vault credentials.

## Configure pull request

### Template

Pull request descriptions should follow a template.
The template generally looks like this:

```markdown
Jira issue: [ABC-???](https://example.atlassian.net/browse/ABC-???)

### Added

- Added some feature so that a business or technical goal could be achieved

### Changed

- Changed previous intentional behavior so that a business or technical goal could work in a new way instead

### Fixed

- Fixed previous unintentional behavior so that intended business or technical behavior occurs instead

### Removed

- Removed previous capability because reasons why capability is no longer needed
```

### Merge strategy

When merging pull requests, the squash merge is typically preferred.
The resulting commit message with its link to the pull request works well with GitHub's generated release notes.
