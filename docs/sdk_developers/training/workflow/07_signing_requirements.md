# DCO and Commit Signing Requirements

PyASL Studio uses the [Developer Certificate of Origin (DCO)](https://developercertificate.org/) to ensure that contributors certify their right to submit their contributions under the project's licensing terms.

Every commit submitted through a pull request must contain a valid `Signed-off-by` line. Git commits can also be GPG-signed so that GitHub can verify the identity of the commit author.

## What are DCO and GPG Signing?

### DCO sign-off

The DCO is a certification that you have the right to submit your contribution under the project's licensing terms.

A DCO sign-off is added to a commit message as:

```text
Signed-off-by: Your Name <your.email@example.com>
```

You add this line using the `-s` option:

```bash
git commit -S -s -m "Your commit message"
```

The `-s` option adds the `Signed-off-by` line automatically.

### GPG signing

GPG signing cryptographically verifies that a commit was created by the identity associated with the signing key.

DCO sign-off and GPG signing are separate:

- **DCO sign-off (`-s`)** adds the `Signed-off-by` line required by the project.
- **GPG signing (`-S`)** cryptographically signs the commit.
- Using `-S -s` provides both.

## Why are they needed?

Every commit in a pull request must have a valid DCO sign-off. The project uses the **DCO-2 GitHub App** to validate this requirement.

GPG signing is used to verify the identity associated with a commit and can make commits appear as **Verified** on GitHub.

Contributors do not need to configure a separate GitHub Actions workflow for DCO validation.

## Initial Setup

Complete the following setup before creating signed commits.

### 1. Configure your Git identity

Configure the name and email address you use for your contributions:

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

The email address should be associated with your GitHub account.

### 2. Generate a GPG key

If you do not already have a GPG key:

```bash
gpg --full-generate-key
```

List your keys:

```bash
gpg --list-secret-keys --keyid-format=long
```

Note the key ID shown after `rsa4096/` or the corresponding key type.

### 3. Configure Git to use the GPG key

Configure Git with your key ID:

```bash
git config --global user.signingkey YOUR_KEY_ID
git config --global commit.gpgsign true
```

With this configuration, Git can automatically GPG-sign commits.

### 4. Add the GPG key to GitHub

Export your public key:

```bash
gpg --armor --export YOUR_KEY_ID
```

Copy the complete output and add it to your GitHub account under:

**Settings → SSH and GPG keys**

The email address associated with the GPG key must also be associated with your GitHub account for GitHub to recognize the signature correctly.

Once configured, signed commits can appear as **Verified** on GitHub.

## Creating Signed Commits

After the initial setup, create commits using:

```bash
git commit -S -s -m "Your commit message"
```

The:

- `-S` option adds a GPG signature.
- `-s` option adds the DCO `Signed-off-by` line.

You can verify the DCO sign-off with:

```bash
git show -s --format=%B HEAD
```

The output should contain:

```text
Signed-off-by: Your Name <your.email@example.com>
```

Every commit included in a pull request should contain a valid sign-off.

## DCO-2 Check

DCO validation is handled by the project's **DCO-2 GitHub App**.

The check runs when a pull request is opened or updated and verifies that the commits included in the pull request contain the required DCO sign-off.

If a commit is missing the sign-off, the DCO check will fail and the affected commit must be corrected.

## Fixing Existing Commits

### Fixing the latest commit

If the latest commit is missing a DCO sign-off or GPG signature, amend it with:

```bash
git commit --amend -S -s --no-edit
```

Verify the commit:

```bash
git show -s --format=%B HEAD
```

If the commit has already been pushed, update the remote branch with:

```bash
git push --force-with-lease
```

Prefer `--force-with-lease` over `--force` when rewriting history.

### Fixing multiple commits

If multiple commits are missing the required sign-off or GPG signature, use an interactive rebase.

For example, to review the last three commits:

```bash
git rebase -i HEAD~3
```

Change `pick` to `edit` for the commits that need to be corrected.

When Git stops at a commit, run:

```bash
git commit --amend -S -s --no-edit
git rebase --continue
```

Repeat this for each affected commit.

After the rebase completes, push the rewritten history:

```bash
git push --force-with-lease
```

Rewriting commits changes their commit hashes. If a pull request has already been reviewed, avoid rewriting its history unless necessary and inform the maintainers when doing so.

## Updating an Already-Pushed Branch

Amending or rebasing commits changes their hashes. If the branch has already been pushed, the rewritten history must be pushed using:

```bash
git push --force-with-lease
```

Use `--force-with-lease` instead of:

```bash
git push --force
```

`--force-with-lease` provides protection against overwriting remote changes that you do not have locally.

## Common Mistakes

### Forgetting the DCO sign-off

A commit created with:

```bash
git commit -m "Your commit message"
```

does not automatically contain a DCO sign-off.

Use:

```bash
git commit -S -s -m "Your commit message"
```

instead.

### GPG key is not associated with GitHub

If a GPG-signed commit does not show as `Verified`, check that:

- The public GPG key has been added to GitHub.
- The email associated with the key is associated with your GitHub account.
- Git is configured to use the expected signing key.

You can check the configured key with:

```bash
git config --global --get user.signingkey
```

### Forgetting to update rewritten history

After amending or rebasing commits that have already been pushed, remember to update the remote branch:

```bash
git push --force-with-lease
```

### Rewriting a reviewed pull request

Amending or rebasing commits changes their hashes and can affect an already-reviewed pull request.

Avoid rewriting reviewed history unless necessary and inform the maintainers when doing so.

## Recommended Workflow

For a new contribution:

1. Create a feature branch.
2. Configure your Git identity.
3. Generate and configure a GPG key if required.
4. Associate the GPG public key with GitHub.
5. Make your changes.
6. Create commits using `git commit -S -s`.
7. Verify that commits contain `Signed-off-by`.
8. Push the branch and open or update the pull request.
9. Check the DCO-2 status.
10. If the check fails, correct the affected commits and update the branch using `git push --force-with-lease` when necessary.