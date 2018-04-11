Red Hat Subscription
====================
[![Build Status](https://travis-ci.org/devnullcake/ansible-role-redhat-subscription.svg?branch=master)](https://travis-ci.org/devnullcake/ansible-role-redhat-subscription) [![Ansible Role](https://img.shields.io/ansible/role/24717.svg)](https://galaxy.ansible.com/devnullcake/redhat-subscription/)

An opinionated role using ansible modules [redhat_subscription](http://docs.ansible.com/ansible/latest/modules/redhat_subscription_module.html) and [rhsm_repository](http://docs.ansible.com/ansible/latest/modules/rhsm_repository_module.html) (new in Ansible 2.5) to allow for the easy registration of a host. Supports using either (activation key, organisation id) or (username, password) combinations.

In addition to registration, repositories can be disabled and/or enabled during execution of this role.

Note that this role has been developed for convenience in majority simple use case. And is not intended to be a catch-all role for subscription management.

Requirements
------------

No additional requirements are enforced other than an an Ansbile version greater than 2.5. Development was done on Ansible 2.5+.

Role Variables
--------------

This role supports the following configurations via variables.

* `redhat_subscription_org_id`: Subscription organisation ID. If using an activation key, this is required.
* `redhat_subscription_activationkey`: Activation key to use for host registration.
* `redhat_subscription_username`: If not using an activation key, specify Red Hat username. 
* `redhat_subscription_password`: If not using an activation key, specify Red Hat password. This is required if using credentials to register the host.
* `redhat_subscription_state`: This variable can be used to specify if you want the subscription removed or added. This is passed directly to the state argument of the [redhat_subscription module](http://docs.ansible.com/ansible/latest/modules/redhat_subscription_module.html). Defaults to `present`.
* `redhat_subscription_pool_regex`: Pool regex to use when registering the host. This is passed directly to the state argument of the [redhat_subscription module](http://docs.ansible.com/ansible/latest/modules/redhat_subscription_module.html). Defaults to `^$`.
* `redhat_subscription_auto_attach`: Supported only when using credentials to register. Defaults to `no`.
* `redhat_subscription_disable_repos`: Repositories to disable. Defaults to `[]`.
* `redhat_subscription_enable_repos`: Repositories to enable. Defaults to `[]`. 
* `redhat_subscription_enable_explicit_repos_only`: This flag, if set, will explicitly disable all repositories that are not specified in `redhat_subscription_enable_repos`.
* `redhat_subscription_skip_prereq`: Skip all prerequisite checks.
* `redhat_subscription_force`: Force registration. This is passed through to the underlying ansible module.
* `redhat_subscription_retry`: Number of times to retry registering the host. Defaults to 0.

Dependencies
------------

No Dependencies required other than Ansible itself.

Example Playbook
----------------

Here is an example play that implements the [Host Preparation](https://access.redhat.com/documentation/en-us/openshift_container_platform/3.9/html-single/installation_and_configuration/#host-registration) steps associated with an OpenShift 3.9 cluster.

    ---
    - name: prepare subscriptions for openshift node
      hosts: all
      become: yes
      tasks:
        - include_role:
             name: devnullcake.redhat-subscription
          vars:
            redhat_subscription_org_id: 123456
            redhat_subscription_activationkey: foobar-activation-key
            redhat_subscription_pool_regex: "^.*OpenShift.*$"
            redhat_subscription_enable_explicit_repos_only: "yes"
            redhat_subscription_disable_repos: []
            redhat_subscription_enable_repos:
              - rhel-7-server-rpms
              - rhel-7-server-extras-rpms
              - rhel-7-server-ose-3.9-rpms
              - rhel-7-fast-datapath-rpms
              - rhel-7-server-ansible-2.4-rpms

Testing
-------
There are no automated tests defined for this role at the moment. Molecule tests will be added at a later date. Current `tox` execution validates source using [ansible-lint](https://github.com/willthames/ansible-lint).

### Tox
This project has [tox](http://tox.readthedocs.io/en/latest/) configured to run against a clean environment. This can simply be run using tox.

```sh
tox
```

License
-------

Apache License 2.0
