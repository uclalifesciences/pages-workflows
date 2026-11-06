# pages-workflows

Shared GitHub Actions workflow for **pages.lifesci.ucla.edu**. Site repositories call it; they never contain deploy logic of their own.

## What it does

1. Runs only for a push (or manual run) on `main`, on the LSC on-prem runner (label `lsc-pages`).
2. Builds the site in a throwaway Podman container (`node:22` for Astro, a Hugo image for Hugo, nothing for plain HTML).
3. Deploys with the site's own key, which can only write to that site's directory on the web nodes.

## Organization setup (LSC, once)

- Create this repo as `<org>/pages-workflows` and tag a release `v1`.
- In this repo: **Settings → Actions → General → Access** → allow access from repositories in the organization.
- Org variable `PAGES_HOSTS` = the web nodes, space separated.
- Put the on-prem runner in a runner group limited to the approved site repositories.
- Org setting: require approval for workflows from outside collaborators/forks.

## Adding a site (LSC)

1. Add the site to `group_vars/all/sites.yml` in the Ansible project.
2. `scripts/new-site-key.sh <dept/site> <org/repo>`: creates the key and sets the repo's `DEPLOY_KEY` secret.
3. `ansible-playbook pages.yml`.
4. Give the site owner the matching template from `site-templates/`.
