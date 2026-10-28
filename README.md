# PHP Coding Standard

## Linting with PHP Code Sniffer

1. Install Code Sniffer: `composer require --dev squizlabs/php_codesniffer`
1. Install the Equisoft ruleset: `composer require --dev kronostechnologies/php-code-standard`
1. Add a `phpcs.xml` file at the root of your project:
    ```xml
    <?xml version="1.0"?>
    <ruleset>
        <rule ref="./vendor/kronostechnologies/php-coding-standard/phpcs.xml"/>
    </ruleset>
    ```
1. Add an entry in your Makefile:
    ```makefile
    check: phpcs

    phpcs:
    	./vendor/bin/phpcs --standard="./phpcs.xml" -p -s .
    ```
1. Configure CircleCI with the phpcs Orb
   [Orb](https://github.com/kronostechnologies/circleci-orbs/blob/master/src/jobs/phpcs.yml)

## Building & Publishing

Apply your changes, merge in `master` branch, then execute the following commands from the root:

```bash
# Mirrors php-coding-standard repository in kronostechnologies/php-coding-standard
./bin/split.sh

# Push tag to kronostechnologies/php-coding-standard
./bin/release.sh v(MAJOR.MINOR.PATCH)
```

To get the latest tag without opening the php-coding-standard repository, checkout the file [.tag](./.tag).

Follow [Semantic Versioning](https://semver.org/).

Afterwards, it is automatically published through packagist.

## Agent tooling (APM)

Install [APM](https://microsoft.github.io/apm/) at the version in `.apm-version`
(currently `0.35.0`), then run from the repository root:

```bash
apm install
apm audit --ci --no-cache
```

The manifest installs the common Equisoft AWT packages and PHP guidance from
`kronostechnologies/agent-toolkit`, pinned together to v1.3.0's commit.
Local authentication must allow reading both `agent-toolkit` and
`kronostechnologies/.github-private` (the organization policy), using `gh auth login`
or a read-only `GITHUB_APM_PAT`. Never commit credentials.

To update, align every toolkit dependency in `apm.yml` to the same release commit,
run `apm install` and the audit again, and commit `apm.lock.yaml` together with all
generated files. Do not edit deployed instructions, prompts, agents, skills, or
`.github/mcp.json` directly; update their source package and reinstall.
`apm_modules/` is local and ignored. This tooling does not change PHPCS behavior.

CI uses `equisoft-actions/setup-apm@v1`, the pinned `.apm-version`, organization
variable `APM_APP_ID`, and secret `APM_APP_PRIVATE_KEY`. The app needs read access
to `agent-toolkit` and `.github-private`; the default `GITHUB_TOKEN` is insufficient.
Ask an organization administrator to grant access if setup fails.

The generated Atlassian MCP configuration requires its own OAuth login to
`https://equisoft.atlassian.net/`. Hosted Copilot code review needs separate
read-only Jira Cloud authentication; see the
[Jira package guide](https://github.com/kronostechnologies/agent-toolkit/blob/v1.3.0/plugins/jira/README.md).
