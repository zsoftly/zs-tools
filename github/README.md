# GitHub repository management

`manage-repository.sh` creates and hardens a GitHub repository from a five-column TSV registry.

## Prerequisites

Use Bash and an authenticated `gh` session with repository creation and administration access. Node.js is required for tests.

## Registry

Columns are `name`, `visibility`, `profile`, `state`, and `purpose`. `profile` is nonempty metadata. Visibility is `public` or `private`; state is `proposed` or `existing`. GitHub remains the naming authority. The script only rejects unsafe local owner and repository segments.

For the `zsoftly` owner, a `proposed` row must also follow the business unit rule: `zcp-<name>` only for a repository that is exclusively about the ZSoftly Cloud Platform, `zs-<name>` for company-wide work including all AWS work, `zps-<client>-<project>` for client work, and `zbc-<name>` for the bootcamp. Names are lowercase kebab-case. `.github` and `existing` rows are exempt. Never reuse the old name of a renamed repository: it would break GitHub's redirect.

```tsv
# name	visibility	profile	state	purpose
sample-app	public	source	proposed	Sample application
private-app	private	distribution	proposed	Private distribution
```

Save the example as `repositories.tsv`. Run these commands from the tools checkout root.

## Use

```bash
./github/manage-repository.sh --owner example-org --registry ./repositories.tsv --repository sample-app
./github/manage-repository.sh --owner example-org --registry ./repositories.tsv --repository sample-app --apply
./github/manage-repository.sh --owner example-org --registry ./repositories.tsv --repository sample-app --verify
./github/manage-repository.sh --owner example-org --registry ./repositories.tsv --repository sample-app --apply --resume
```

The default is a read-only preview. `--apply` creates a repository from a `proposed` row. `--resume` continues hardening after an interrupted apply. Existing rows support preview and verification only.

For every repository, the tool disables Actions, projects, discussions, auto-merge, and non-squash merges. Public repositories also receive secret scanning, push protection, private vulnerability reporting, immutable releases, and default-branch protection. Private repositories skip private branch protection and secret-scanning controls.

```bash
npm run check:repository
```
