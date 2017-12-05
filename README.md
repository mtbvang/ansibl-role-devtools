ansible-role-devtools
=========

Install development tools on Centos and Ubuntu host OSes.

Requirements
------------

Virtualbox and/or VMWare workstation
Vagrant

Role Variables
--------------

Look in vars and defaults folders.

Dependencies
------------

Look in requirements.yml

Example Playbook
----------------

Look at tests/test.yml

Running Tests
-------------

The molecule testing framework doesn't seem to be working properly. I have posted a question on https://groups.google.com/forum/#!topic/molecule-users/26jJPT-apbU.

Tests can also be run through the gradle tasks that use vagrant.

To run all tasks against the default vagrant provider and the ubuntu VM e.g.:
´´´
./gradlew testintegration -PvagrantVMName=ubuntu
´´´

To run a subset of tasks use the ansibleTags parameter to only call a subset of Ansible tasks that have been tagged e.g.:
´´´
./gradlew testintegration -PvagrantVMName=ubuntu -PansibleTags=java
´´´

To skip a gradle dependency use the -x flage e.g.:
´´´
./gradlew testintegration -PvagrantVMName=ubuntu -PansibleTags=all -x ansibleRolesCleanAndInstall
´´´

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
