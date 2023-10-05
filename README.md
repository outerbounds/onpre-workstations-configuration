Instructions
============

To run/deploy Ansible in order to configure an on-premise machine follow these steps:

1. Install Ansible on you local machine. You can use Homebrew or other methods document [here](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html).

1. Ensure the target in `inventory.yam` have all the nodes you would like to target. Example setup

    ```yaml
        ...
        machine_2:                      # target name
            ansible_host: 10.10.34.56   # target host ip or DNS
            ansible_user: ubuntu        # ssh username
    ```
1. Update the JWT issuer URL and expected audience variables in `playbook.yaml`

    ```yaml
        ...
        - name: ansible-role-onprem-workstations
        vars:
            outerbounds_issuer: <the issuer URL> # This will be provided to you by Outerbounds
            outerbounds_audience: <the expected jwt audience> # This will be provided to you by Outerbounds
        ...
    ```

1. Install the external Ansible roles:

    ```bash
    $ ansible-galaxy install -r requirements.yml --force
    ```

1. Ensure you have ssh access to all the target nodes in the inventory list. If so you can now deploy the playbok like:

    ```bash
    $ # to deploy to all nodes
    $ ansible-playbook -i inventory.yaml playbook.yaml

    $ # to deploy to a single target
    $ ansible-playbook -i inventory.yaml -l <target-name> playbook.yaml
    ```

Troubleshooting
===============

1. If Ansible gets stuck on the "Gather Facts" step you can try to remove the cache by deleting the `~/.ansible/` folder and rerun the playbook. You may also need to reinstall the Ansible roles using `ansible-galaxy install -r requirements.yml --force`.

2. If your local machine doesn't have nvidia drivers and would like them to be enabled as part of the same Ansible run, you and enable reboot by updating the `playbook.yaml` as shown below.
    ```yaml
    ...
    - name: nvidia_driver
      vars:
        nvidia_driver_skip_reboot: no
    ...
    ```

    **Note:** this variables is disabled in the playbook to avoid forcing a reboot.
