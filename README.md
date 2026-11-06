# pages-workflows

Shared GitHub Actions workflow for **pages.lifesci.ucla.edu**. Site repositories call it; they never contain deploy logic of their own.

## What it does

1. Runs only for a push (or manual run) on `main`, on the LSC on-prem runner (label `lsc-pages`).
2. Builds the site in a throwaway Podman container (`node:22` for Astro, a Hugo image for Hugo, nothing for plain HTML).
3. Deploys with the site's own key, which can only write to that site's directory on the web nodes.

## Organization setup (LSC, once)

Works on the GitHub **Free** plan:
- Create this repo as `uclalifesciences/pages-workflows`, **public** (no secrets in it), and tag a release `v1`.
- Site repositories are **private**. On Free, org-level secrets and variables are not visible to private repos, so nothing is stored at org level: each site repo has its own `DEPLOY_KEY` secret, and the list of web nodes lives on the runner (`PAGES_HOSTS`, set by Ansible).
- On Free there is only the default runner group: keep its "Allow public repositories" option **off**, so only the org's private repos can use the on-prem runner.
- Org setting: require approval for workflows from outside collaborators/forks.

## Adding a site (LSC)

1. Add the site to `group_vars/all/sites.yml` in the Ansible project.
2. `scripts/new-site-key.sh <dept/site> <org/repo>`: creates the key and sets the repo's `DEPLOY_KEY` secret.
3. `ansible-playbook pages.yml`.
4. Give the site owner the matching template from `site-templates/`.
