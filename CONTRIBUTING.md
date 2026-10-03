# Contributing

Thank you for contributing to PyASL Studio!

## Developer Certificate of Origin (DCO)

All commits submitted through pull requests must include a valid
`Signed-off-by` line.

This certifies that you have the right to submit the contribution
under the project's licensing terms.

A signed-off commit looks like:

```text
Signed-off-by: Your Name <your.email@example.com>
```

### Configure Git

Make sure Git is configured with your name and email:

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

You can verify your configuration with:

```bash
git config --global --list
```

### Sign Off Commits

Use the `-s` option when creating a commit:

```bash
git commit -s -m "your commit message"
```

Git will automatically add the `Signed-off-by` line to your commit.

For example:

```bash
git commit -s -m "Add DCO enforcement"
```

Before opening a pull request, make sure **every commit in your branch**
contains a valid `Signed-off-by` line.