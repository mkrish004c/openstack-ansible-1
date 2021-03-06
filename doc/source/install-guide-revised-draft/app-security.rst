=====================
Appendix D: Security
=====================

The OpenStack-Ansible project provides several security features for
OpenStack deployments. This section of documentation covers those
features and how they can benefit deployers.

Security requirements always differ between deployers. If you require
additional security measures, refer to the official
`OpenStack Security Guide`_ for additional resources.

AppArmor
~~~~~~~~

The Linux kernel offers multiple `security modules`_ (LSMs) that that set
`mandatory access controls`_ (MAC) on Linux systems.  The OpenStack-Ansible
project configures `AppArmor`_. AppArmor is a Linux security module that
provides additional security on LXC container hosts. AppArmor allows
administrators to set specific limits and policies around what resources a
particular application can access. Any activity outside the allowed policies
is denied at the kernel level.

AppArmor profiles that are applied in OpenStack-Ansible limit the actions
that each LXC container may take on a system. This is done within the
`lxc_hosts role`_.

.. _security modules: https://en.wikipedia.org/wiki/Linux_Security_Modules
.. _mandatory access controls: https://en.wikipedia.org/wiki/Mandatory_access_control
.. _AppArmor: https://en.wikipedia.org/wiki/AppArmor
.. _lxc_hosts role: https://github.com/openstack/openstack-ansible-lxc_hosts

Encrypted communication
~~~~~~~~~~~~~~~~~~~~~~~

Data in transit is encrypted between some OpenStack services in
OpenStack-Ansible deployments. For more details on what traffic is encrypted,
and how to configure SSL certificates, see
`Securing services with SSL certificates`_.

.. _Securing services with SSL certificates: app-advanced-config-sslcertificates.html

Host security hardening
~~~~~~~~~~~~~~~~~~~~~~~

Security hardening is applied by default to OpenStack infrastructure and
compute hosts using the ``openstack-ansible-security`` role. The purpose of
the role is to apply as many security configurations as possible without
disrupting the operation of an OpenStack deployment.

Refer to the documentation on :ref:`security_hardening` for more information
on the role in OpenStack-Ansible.

Least privilege
~~~~~~~~~~~~~~~

The `principle of least privilege`_ is used throughout OpenStack-Ansible to
limit the damage that could be caused if an attacker gains access to any
credentials.

OpenStack-Ansible configures unique username and password combinations for
each service that talks to RabbitMQ and Galera/MariaDB. Each service that
connects to RabbitMQ uses a separate virtual host for publishing and consuming
messages. The MariaDB users for each service are only granted access to the
database(s) that they need to query.

.. _principle of least privilege: https://en.wikipedia.org/wiki/Principle_of_least_privilege

.. _least-access-openstack-services:

Securing network access to OpenStack services
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

OpenStack environments expose many service ports and API endpoints to the
network.

.. note::

   Deployers must limit access to these resources and expose them only
   to trusted users and networks.

The resources within an OpenStack environment can be divided into two groups:

1. Services that users must access directly to consume the OpenStack cloud.

   * aodh
   * cinder
   * ceilometer
   * glance
   * gnocchi
   * heat
   * horizon
   * ironic
   * keystone *(excluding the admin API endpoint)*
   * neutron
   * nova
   * swift

2. Services that are only utilized internally by the OpenStack cloud.

   * keystone (admin API endpoint)
   * MariaDB
   * memcached
   * RabbitMQ

Configure firewalls to limit network access to all services that users must
access directly.

Other services, such as MariaDB and RabbitMQ, must be segmented away from
direct user access. Configure a firewall to only allow connectivity to
these services within the OpenStack environment itself. This
reduces an attacker's ability to query or manipulate data in OpenStack's
critical database and queuing services, especially if one of these services
has a known vulnerability.

For more details on recommended network policies for OpenStack clouds, refer
to the `API endpoint process isolation and policy`_ section from the
`OpenStack Security Guide`_

.. _API endpoint process isolation and policy: http://docs.openstack.org/security-guide/api-endpoints/api-endpoint-configuration-recommendations.html#network-policy
.. _OpenStack Security Guide: http://docs.openstack.org/security-guide

--------------

.. include:: navigation.txt
