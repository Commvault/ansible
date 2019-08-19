Ansible Commvault module
========================

`Ansible <https://www.ansible.com/>`_ is a radically simple IT automation engine that automates cloud provisioning, configuration management, application deployment, intra-service orchestration, and many other IT needs.

`How Ansible Works | Ansible.com <https://www.ansible.com/overview/how-ansible-works>`_

`Getting started with Ansible <https://www.linode.com/docs/applications/configuration-management/getting-started-with-ansible/>`_

`Getting started with playbooks <https://www.digitalocean.com/community/tutorials/configuration-management-101-writing-ansible-playbooks>`_

Introduction
------------

Ansible Commvault module can be used in playbooks to automate commvault operations



Ansible is supported only for Linux machines

Commvault module uses `CVPySDK <https://github.com/CommvaultEngg/cvpysdk>`_ to perform operations

CVPySDK, in turn, uses Commvault REST API to perform operations on a Commcell via WebConsole.


Requirements
------------

- Ansible
- Python 2.7 or above
- `CVPySDK <https://github.com/CommvaultEngg/cvpysdk>`_
- Commvault Software v11 SP16 or later release with WebConsole installed

Installing CVPySDK
------------------

CVPySDK can be installed directly from PyPI using pip:

    >>> pip install cvpysdk


CVPySDK is available on GitHub `here <https://github.com/CommvaultEngg/cvpysdk>`_

It can also be installed from source.

After downloading, from within the ``cvpysdk`` directory, execute:

    >>> python setup.py install

Installing Ansible
------------------

- `Installation guide | Ansible.com <https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html>`_


Using Ansible commvault module
------------------------------

**Login to Commcell:**
::

  ---
  - name: Commvault Ansible
    gather_facts: no
    hosts: localhost
    connection: local

    vars:
    webconsole_hostname: 'webconsole_hostname'
    commcell_username: 'commcell_username'
    commcell_password: 'commcell_password'

    tasks:
      - name: Login
        commvault:
            operation: login
            entity: {
            webconsole_hostname: "{{ webconsole_hostname }}",
            commcell_username: "{{ commcell_username }}",
            commcell_password: "{{ commcell_password }}"
            }
        register: commcell

**Run backup for a subclient:**
::

  - name: Backup
    commvault:
            operation: "backup"
            entity_type: subclient
            commcell: "{{ commcell }}"
            entity: {
            client: "client name",
            agent: "file system",
            backupset: "defaultbackupset",
            subclient: "default"
            }
    register: backup_job

**Run restore in place job for a subclient:**
::

 - name: Restore
   commvault:
        operation: "restore_in_place"
        entity_type: subclient
        commcell: "{{ commcell }}"
        entity: {
        client: "client name",
        agent: "file system",
        backupset: "defaultbackupset",
        subclient: "default"
        }
        args: {
        paths: ['path']
        }
   register: restore_job
          
**Wait for the restore job to complete:**
::

  - name: wait for restore job to complete
    commvault:
        operation: "wait_for_completion"
        entity_type: "job"
        commcell: "{{ commcell }}"
        entity: {
        job_id: "{{ restore_job.output }}"
        }
    register: restore_status

Explanation:
------------

**operation** corresponds to a method name in CVPySDK modules, example "restore_in_place" method is in subclient.py module

**entity_type** corresponds to baisc CVPySDK class, available options are

- Commcell
- Client
- Clientgroup
- Agent
- Instance
- Backupset
- Subclient
- Job

**commcell** is mandatory to perform any tasks, when performing login operation commcell is registered and can later be used in other tasks

**entity** will contain basic CVPySDK inputs, available options are

- client
- clientgroup
- agent
- instance
- backupset
- subclient
- job_id

**args** contains the arguments to be passed to the method

Contribution Guidelines
-----------------------

#. We welcome all the enhancements from everyone although we request the developer to follow some guidelines while interacting with the ``Ansible commvault module`` codebase.

#. Before adding any enhancements/bug-fixes, we request you to open an Issue first.

#. The core team will go over the Issue and notify if it is required or already been worked on.

#. If the Issue is approved, the contributor can then make the changes to their fork and open a pull request.

Coding Considerations
*********************

- All python code should be **PEP8** compliant.
- All changes should be consistent with the design of the SDK.
- The code should be formatted using **autopep8** with line-length set to **119** instead of default **79**.
- All changes and any new methods/classes should be properly documented.
- The docstrings should be of the same format as existing docs.

Code of Conduct
***************

Everyone interacting in the **Ansible commvault module** project's codebases, issue trackers,
chat rooms, and mailing lists is expected to follow the
`PyPA Code of Conduct`_.

.. _PyPA Code of Conduct: https://www.pypa.io/en/latest/code-of-conduct/

License
-------
**CVPySDK** and **Commvault ansible module** are licensed under `Apache 2.0 <https://raw.githubusercontent.com/CommvaultEngg/cvpysdk/master/LICENSE.txt>`_

About Commvault
---------------
.. image:: https://upload.wikimedia.org/wikipedia/en/thumb/1/12/Commvault_logo.png/150px-Commvault_logo.png
    :align: center

|

`Commvault <https://www.commvault.com/>`_
(NASDAQ: CVLT) is a publicly-traded data protection and information management software company headquartered in Tinton Falls, New Jersey.

It was formed in 1988 as a development group in Bell Labs, and later became a business unit of AT&T Network Systems. It was incorporated in 1996.

Commvault software assists organizations with data backup and recovery, cloud and infrastructure management, and retention and compliance.
