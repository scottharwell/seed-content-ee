# Ansible Seeded Content Execution Environment

Provided as an example of packing an execution environment that can be used to seed Ansible Automation Controller with content.

## Implementation

This EE packages all of the dependencies required to seed Ansible Automation Controller with opinionated / pre-build content that can be used for example purposes or production use cases.

### Collections Included

| Name                         | Purpose                                                                                 |
| ---------------------------- | --------------------------------------------------------------------------------------- |
| `ansible.controller`         | Used for Automating Ansible Automation Controller                                       |
| `awx.awx`                    | Used for automating AWX                                                                 |
| `aoc.controller_demo_config` | A collection that implements roles that seed Ansible Automation Controller with content |

### Roles Used

The included playbook calls the following roles to seed content.

| Name                                    | Purpose                                                                                                                                                                      |
| --------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `aoc.controller_demo_config.controller` | A role that demonstrates creating many different record types in Automation Controller.  It is primarily used by the Ansible on Clouds team to configure a demo environment. |

## Instructions

### Ansible Navigator

This repository also contains an example playbook that can be run through the Ansible Navigator CLI, or as a template in Ansible Automation controller.  The env vars and parameters are used in [the role](https://github.com/ansible-content-lab/aoc.controller_demo_config) that the playbook imports, but the example below contains dummy values.  See [the README](https://github.com/ansible-content-lab/aoc.controller_demo_config) for the role regarding all of the variables that can be set.

```bash
ansible-navigator run playbook_seed_content.yml \
--pae false \
--mode stdout \
--ee true \
--ce docker \
--eei quay.io/scottharwell/seed-content-ee:latest \
--pp always \
--eev $HOME/.ssh:/home/runner/.ssh \
--senv "CONTROLLER_HOST=controller.123456.ansiblecloud.redhat.com" \
--penv CONTROLLER_USERNAME \
--penv CONTROLLER_PASSWORD \
--penv AZURE_TENANT \
--penv AZURE_SUBSCRIPTION_ID \
--penv AZURE_CLIENT_ID \
--penv AZURE_SECRET \
--penv RED_HAT_ACCOUNT \
--penv RED_HAT_PASSWORD \
--senv "SOCIAL_AUTH_AZUREAD_OAUTH2_KEY=F2B50D03-328A-47F5-AAB9-D2DEB65F570D" \
--senv "SOCIAL_AUTH_AZUREAD_OAUTH2_SECRET=39257D63-F3AA-43CF-B1B6-33B7A30C7FAD" \
--extra-vars "{'job_templates': {'ssh_public_key': \"{{ lookup('file','/home/runner/.ssh/id_rsa_azure_demo.pub') }}\", 'admin_password': 'ansible123456' }}" \
--extra-vars "{'credentials': { 'ssh_key_data': \"{{ lookup('file','/home/runner/.ssh/id_rsa_azure_demo') }}\" }}" \
--extra-vars "session_cookie_age=28800" \
--extra-vars "azure_resource_group=sh-rg-peering-demo"
```

### Ansible

It is also possible to run this seeded content container without Ansible CLIs locally.  Follow these steps to use your local container engine to deploy seeded content.  Note that `docker` and `podman` can be used interchangeably in this example.

1. Enter the container locally on your PC.

    ```bash
    docker run -it --rm --pull always -v $HOME/.ssh:/home/runner/.ssh quay.io/scottharwell/seed-content-ee:latest /bin/bash
    ```

2. Set any environment variables required by the role.

    ```bash
    export CONTROLLER_HOST="controller.123456.ansiblecloud.redhat.com"
    export CONTROLLER_ADMIN="admin"
    ...
    ```

3. Run the playbook.

    ```bash
    ansible-playbook playbook_seed_content.yml \
    --extra-vars "{'job_templates': {'ssh_public_key': \"{{ lookup('file','/home/runner/.ssh/id_rsa_azure_demo.pub') }}\", 'admin_password': 'ansible123456' }}" \
    --extra-vars "{'credentials': { 'ssh_key_data': \"{{ lookup('file','/home/runner/.ssh/id_rsa_azure_demo') }}\" }}" \
    --extra-vars "session_cookie_age=28800" \
    --extra-vars "azure_resource_group=sh-rg-peering-demo"
    ```
