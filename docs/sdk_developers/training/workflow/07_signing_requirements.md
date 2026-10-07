# DCO and Commit Signing Requirements

PyASL Studio uses the [Developer Certificate of Origin (DCO)](https://developercertificate.org/) to ensure that contributors certify their right to submit their contributions under the project's licensing terms.

Every commit submitted through a pull request must contain a valid `Signed-off-by` line.

## DCO Sign-Off

A signed-off commit contains a line such as:

```text
Signed-off-by: Your Name <your.email@example.com>
```

The easiest way to add this line is to use the `-s` option when committing:

```bash
git commit -s -m "Your commit message"
```

Git automatically adds the `Signed-off-by` line to the commit message.

You can check the latest commit with:

```bash
git show -s --format=%B HEAD
```

Every commit included in a pull request should contain a valid sign-off.

## DCO Check

DCO validation is handled by the project's **DCO-2 GitHub App**.

The check runs when a pull request is opened or updated and verifies that the commits included in the pull request contain the required DCO sign-off.

Contributors do not need to configure a separate GitHub Actions workflow for DCO validation.

## Fixing a Missing Sign-Off

### Latest commit

If the latest commit is missing a sign-off, amend it with:

```bash
git commit --amend -s --no-edit
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

### Multiple commits

If multiple commits are missing sign-offs, use an interactive rebase.

For example, to review the last three commits:

```bash
git rebase -i HEAD~3
```

Change `pick` to `edit` for the commits that need to be corrected.

When Git stops at a commit, run:

```bash
git commit --amend -s --no-edit
git rebase --continue
```

Repeat this for each affected commit.

After the rebase completes, push the rewritten history:

```bash
git push --force-with-lease
```

Rewriting commits changes their commit hashes. If a pull request has already been reviewed, avoid rewriting its history unless necessary and inform the maintainers when doing so.

## GPG-Verified Commits

DCO sign-off and GPG commit signing are separate:

- **DCO sign-off** adds a `Signed-off-by` line to the commit message.
- **GPG signing** cryptographically verifies the identity used to sign the commit.

Both can be used together.

### Generate a GPG key

If you do not already have a GPG key:

```bash
gpg --full-generate-key
```

List your keys:

```bash
gpg --list-secret-keys --keyid-format=long
```

Note the key ID shown after `rsa4096/` (or the corresponding key type).

### Configure Git to sign commits

Configure Git to use your key:

```bash
git config --global user.signingkey YOUR_KEY_ID
git config --global commit.gpgsign true
```

You should still use `-s` when creating commits so that the DCO sign-off is included:

```bash
git commit -s -m "Your commit message"
```

### Add the key to GitHub

Export your public key:

```bash
gpg --armor --export YOUR_KEY_ID
```

Copy the complete output and add it to your GitHub account under **Settings → SSH and GPG keys**.

The email address associated with the GPG key must also be associated with your GitHub account for GitHub to recognize the signature correctly.

Once configured, signed commits can appear as **Verified** on GitHub.

## Common Mistakes

### Forgetting `-s`

A commit created with:

```bash
git commit -m "Your commit message"
```

does not automatically contain a DCO sign-off.

Use:

```bash
git commit -s -m "Your commit message"
```

### GPG key is not associated with GitHub

If a GPG-signed commit does not show as `Verified`, check that:

- The public GPG key has been added to GitHub.
- The email associated with the key is associated with your GitHub account.
- Git is configured to use the expected signing key.

You can check the configured key with:

```bash
git config --global --get user.signingkey
```

### Force-pushing rewritten history

Amending or rebasing commits changes their hashes. When updating a branch that has already been pushed, use:

```bash
git push --force-with-lease
```

instead of:

```bash
git push --force
```

## Recommended Workflow

For a new contribution:

1. Create a feature branch.
2. Configure your Git identity.
3. Optionally configure GPG signing and associate the key with GitHub.
4. Make your changes.
5. Create commits using `git commit -s`.
6. Verify that commits contain `Signed-off-by`.
7. Push the branch and open or update the pull request.
8. Check the DCO-2 status.
9. If the check fails, correct the affected commits and push the updated history with `--force-with-lease` when necessary.