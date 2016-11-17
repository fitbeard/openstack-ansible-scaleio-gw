ScaleIO Gateway for OpenStack-Ansible
#########################################
:date: 2016-11-17
:tags: openstack, ansible
:category: \*openstack, \*nix


About this repository
---------------------

This set of playbooks will deploy ScaleIO Gateway in LXC containers to get high availability of this service in Cinder and Nova.

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

    openstack-ansible openstack-ansible playbook-scaleio-install.yml

------

Install grafana

If you're proxy'ing grafana you will need to provide the full ``root_path`` when you run the playbook add the following ``-e grafana_root_url='https://cloud.something:8443/grafana/'``

.. code-block:: bash

    openstack-ansible playbook-grafana.yml -e galera_root_user=root -e galera_address='127.0.0.1'

Once that last playbook is completed you will have a functioning InfluxDB, Telegraf, and Grafana metric collection system active and collecting metrics. Grafana will need some setup, however functional dash boards have been provided in the ``grafana-dashboards`` directory.

Install Kapacitor

.. code-block:: bash

   openstack-ansible playbook-kapacitor.yml
