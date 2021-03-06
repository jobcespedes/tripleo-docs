.. _composable_services:

Deploying with Composable Services
==================================

TripleO offers the option of deploying with a user-defined list of services
per role (where "role" means group of nodes, e.g "Controller", and "service"
refers to the individual services or configurations e.g "Nova API").


Deploying with custom service lists
-----------------------------------

Each role defines a default list of services, which can be viewed in the
`roles_data.yaml` file (see `/usr/share/openstack-tripleo-heat-templates`, or
the tripleo-heat-templates_ git repository.)

The data in `roles_data.yaml` is used to set the defaults for per-role parameters
e.g `CustomControllerServices`.  These defaults can be overridden via environment
files, e.g::

    cat > keystone_only_params.yaml << EOF

    parameter_defaults:
      CustomControllerServices:
        - OS::TripleO::Services::Keystone
        - OS::TripleO::Services::RabbitMQ
        - OS::TripleO::Services::HAproxy
        - OS::TripleO::Services::MySQL
      ComputeCount: 0
      OvercloudCustomControllerFlavor: customcontroller

    EOF

The example above overrides the default list of services, and instead deploys
Keystone and the services it requires.  It also sets the ComputeCount to zero
to enable a minimal "keystone only" deployment on a single node.

You can then pass the environment file on deployment as follows::

    openstack overcloud deploy -e keystone_only_params.yaml

The same approach can be used for any role.

.. warning::
  While considerable flexibilty is available regarding service placement with
  these interfaces, the flexible placement of pacemaker managed services is only
  available since the Ocata release.

.. warning::
  In general moving control-plane services to the Compute role is not
  recommended, as the compute nodes require a different upgrade lifecycle
  and thus control-plane services on this role may present problems during
  major upgrades between releases.

.. _tripleo-heat-templates: https://git.openstack.org/openstack/tripleo-heat-templates
