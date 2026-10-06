# Ansible Platform Collection

## Changelog for v2.7.20260313

* Added OIDC User Identity support for Ansible Automation Platform Gateway
* New modules: feature_flag, ca_certificates, role_team_assignment, role_definition
* Add request_timeout_seconds and idle_timeout_seconds to route modules
* Add enable_mtls attribute to route module for mutual TLS support
* Add associated_authenticators parameter to users module
* Add Gateway UI plugin Route Collection Module
* Enhanced organization association logic and auditor user support
* Multiple bug fixes for role assignment, object deletion, and URL handling
* Deprecated authenticator_uid and authenticators fields

## Upgrading to AAP 2.7?

Ensure you are on `ansible.platform >= 2.7.x` before upgrading. Connection variables have changed — replace `controller_host` with `aap_hostname` pointing to your gateway URL.

See the [AAP 2.7 documentation](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.7) for the full upgrade guide.

## Description

This collection contains modules that can be used to automate the creation of resources on an install of Ansible Automation Platform.

## Requirements

This collection supports python versions >=3.11 and requires an ansible-core version of >=2.16.0.

It also requires an existing install of Ansible Automation Platform as a target.

## Installation

Before using this collection, you need to install it with the Ansible Galaxy command-line tool:

```
ansible-galaxy collection install ansible.platform
```

You can also include it in a requirements.yml file and install it with ansible-galaxy collection install -r requirements.yml, using the format:

```yaml
collections:
  - name: ansible.platform
```

Note that if you install any collections from Ansible Galaxy, they will not be upgraded automatically when you upgrade the Ansible package.
To upgrade the collection to the latest available version, run the following command:

```
ansible-galaxy collection install ansible.platform --upgrade
```

You can also install a specific version of the collection, for example, if you need to downgrade when something is broken in the latest version (please report an issue in this repository). Use the following syntax to install version 2.5.0:

```
ansible-galaxy collection install ansible.platform:==2.5.0
```

See [using Ansible collections](https://docs.ansible.com/ansible/devel/user_guide/collections_using.html) for more details.

## Use Cases

This collection can be used to automate to the creation of resources inside of the Ansible Automation Platform. Things such as users, organizations and teams can be created using this collection.

Adding services (Controller, Event Driven Automation, Automation) can also be done with this collection. Nodes for those services can also be added.

## Authenticating to AAP in a playbook

Connecting to AAP requires specifying authentication variables (the ones prefixed by `aap_` here) in the task. Alternatively, `AAP_` environment variables can also be set. For a complete list of authentication variables that can be used, please refer to the module specific documentations.

```yaml
- name: Manage AAP
  hosts: localhost
  tasks:
    - name: Example for auth
      ansible.platform.<module-name>:
        your-module-parameters: parameter-values
        aap_hostname: your-hostname
        aap_username: your-username
        aap_password: your-password
```

## Testing

### Unit and Sanity Tests

The collection includes comprehensive unit tests and sanity tests that run automatically on every PR:

```bash
# Run unit tests
pytest tests/unit -v

# Run sanity tests
ansible-test sanity --docker

# Run all molecule tests
molecule test -s <scenario_name>
```

### Integration Tests

Integration tests validate modules against a live Ansible Automation Platform instance. These tests require AAP credentials and are run in three connection modes: `local`, `http-direct`, and `http-persistent`.

#### Running Integration Tests Locally

**Prerequisites:**
- Running Ansible Automation Platform instance (2.7+)
- Valid AAP credentials with admin permissions
- Python 3.11+
- ansible-core 2.16+

**Run all integration tests:**

```bash
# Set AAP connection details
export AAP_HOSTNAME=your-aap-hostname
export AAP_USERNAME=your-username
export AAP_PASSWORD=your-password

# Run integration tests (default: http-persistent mode)
make collection-test

# Or specify connection mode
make collection-test CONNECTION_MODE=local
make collection-test CONNECTION_MODE=http-direct
make collection-test CONNECTION_MODE=http-persistent
```

**Run specific integration test:**

```bash
ansible-test integration organizations_test --docker
ansible-test integration teams_test users_test --docker
```

#### Running Integration Tests in CI

Integration tests in CI run against a live AAP instance and require the **"safe to test"** label.

**For contributors with repository permissions:**

1. Ensure all pre-merge checks pass (unit, sanity, linting)
2. Verify PR doesn't modify workflow files or credential-handling code
3. Apply the **"safe to test"** label to the PR
4. Integration tests will run automatically in all three connection modes
5. Monitor results in the PR checks

**For contributors without label permissions:**

If you don't have permissions to add the "safe to test" label:

1. Request integration tests in a PR comment: `@ansible/platform-maintainers please run integration tests`
2. A collection maintainer will review your PR and apply the label if appropriate
3. Integration tests will run once the label is applied

**Security Note:** The "safe to test" label triggers workflows with access to AAP credentials. Maintainers review PRs before applying this label to ensure no malicious code is executed.

#### Integration Test Coverage

- **Pre-label checks (automatic):** Unit tests, sanity tests (all Ansible versions), linting, molecule tests
- **Post-label checks (manual approval):** Integration tests against live AAP in all connection modes

#### Branch-Specific Testing

The collection maintains multiple branches for different AAP versions. Integration tests vary by branch:

**`devel` branch:**
- Tests against: **AAP 2.7+** (latest features)
- Connection modes: `local`, `http-direct`, `http-persistent`
- Uses Gateway API (`/api/gateway/v1/`)
- Tests new features before backporting to stable branches
- Python: 3.11+
- ansible-core: 2.16+

**`stable-2.7` branch:**
- Tests against: **AAP 2.7.x**
- Connection modes: `local`, `http-direct`, `http-persistent`
- Uses Gateway API (`/api/gateway/v1/`)
- Backported features from devel (after stabilization)
- Python: 3.11+
- ansible-core: 2.16+

**`stable-2.6` branch:**
- Tests against: **AAP 2.6.x**
- Connection modes: `local`, `http-direct` (http-persistent not supported)
- Uses Gateway API (`/api/gateway/v1/`)
- Bug fixes only (feature development frozen)
- Python: 3.11+
- ansible-core: 2.16+

**When contributing:**
- **New features:** Target `devel` branch
- **Bug fixes for AAP 2.7:** Target `devel`, will be backported to `stable-2.7`
- **Bug fixes for AAP 2.6:** Target `stable-2.6` directly (or request backport)

Integration tests for each branch run against the corresponding AAP version to ensure compatibility.

See the [E2E Test Suite Documentation](https://aap-cac-e2e-test-suite-29237a.pages.redhat.com/) for additional end-to-end testing guidance.

The collection is tested against the current version of Ansible Automation Platform.

## Support

This collection is supported by Red Hat Engineering.

- Open a support case at [Red Hat Customer Portal](https://access.redhat.com/support/)
- Report collection bugs or request features using the **Create issue** button on the [Automation Hub collection page](https://console.redhat.com/ansible/automation-hub/repo/published/ansible/platform)
- For community discussion, see the [Ansible Forum](https://forum.ansible.com)

## Release Notes and Roadmap

Changelogs can be found in the [changelogs directory](https://github.com/ansible/ansible.platform/tree/devel/changelogs).

## Related Information

- [Ansible Automation Platform Documentation](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform)
- [Using Ansible Collections](https://docs.ansible.com/ansible/devel/user_guide/collections_using.html)
- [Ansible Certified Collections README Template](https://access.redhat.com/articles/7068606)

## License Information

[GPLv3](https://github.com/ansible/ansible.platform/blob/devel/COPYING)

## Authors

[Sean Sullivan](https://github.com/sean-m-sullivan)
[Martin Slemr](https://github.com/slemrmartin)
[Jake Jackson](https://github.com/thedboubl3j)
[Brennan Paciorek](https://github.com/brennanpaciorek)
[John Westcott](https://github.com/john-westcott-iv)
[Jessica Steurer](https://github.com/jay-steurer)
[Bryan Havenstein](https://github.com/bhavenst)
