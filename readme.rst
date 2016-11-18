ScaleIO Gateway for OpenStack-Ansible
#########################################
:date: 2016-11-18
:tags: openstack, ansible
:category: \*openstack, \*nix


About this repository
---------------------

This set of playbooks will deploy ScaleIO Gateway in LXC containers to get high availability service in OpenStack Cinder and Nova.

Process
-------

Clone this repo

.. code-block:: bash

    cd /opt
    git clone -b <TAG> https://github.com/fitbeard/openstack-ansible-scaleio-gw

Copy the env.d and conf.d files into place

.. code-block:: bash

    cd openstack-ansible-scaleio-gw
    cp etc/env.d/scaleio-gateway.yml /etc/openstack_deploy/env.d/
    cp etc/conf.d/scaleio-gateway.yml /etc/openstack_deploy/conf.d/

Edit ``/etc/openstack_deploy/conf.d/scaleio-gateway.yml`` config depending on environment.

Put ScaleIO Gateway deb package in ``/opt/files`` or other location defined in ``scaleio_install_file_location`` variable in ``defaults/main.yml``.

Add the export to update the inventory file location

.. code-block:: bash

    export ANSIBLE_INVENTORY=/opt/openstack-ansible/playbooks/inventory/dynamic_inventory.py

If you are running the HA Proxy you should run the following playbook as well to enable the ScaleIO Gateway port 9943

.. code-block:: bash

    openstack-ansible playbook-scaleio-haproxy.yml

Create the containers

.. code-block:: bash

    openstack-ansible /opt/openstack-ansible/playbooks/lxc-containers-create.yml -e container_group=scaleio_gateway_server

Install Oracle Java and other required packages

.. code-block:: bash

    openstack-ansible playbook-scaleio-dependencies.yml

Install ScaleIO Gateway

You can change default gateway admin password set in ``defaults/main.yml`` by passing variable to playbook ``-e scaleio_gateway_admin_password=SecurePassword`` or putting it to the same config with rest of openstack-ansible passwords in ``/etc/openstack_deploy/user_secrets.yml``

.. code-block:: bash

    openstack-ansible playbook-scaleio-install.yml
