.. _kibana__ref_dependency:

Usage as a role dependency
==========================

The ``debops.kibana`` role can be used as a dependency by other Ansible roles
to manage Kibana main configuration file idempotently.  Configuration options
from multiple roles can be merged together and included in the configuration
file, or removed conditionally.

.. contents::
   :local:


Dependent role variables
------------------------

The role exposes three default variables that can be used by other Ansible
roles as dependent variables:

``kibana__dependent_name``
  Required. Name of the role that uses the ``debops.kibana`` as a dependency.
  This will be used to store the configuration in its own YAML dictionary. The
  selected name shouldn't be changed, otherwise configuration will be
  desynchronized.

``kibana__dependent_configuration``
  Required. List of the Kibana configuration options defined in the same format
  as the main configuration. See :ref:`kibana__ref_configuration` for more
  details.

``kibana__dependent_state``
  Optional. If not specified or ``present``, the configuration will be included
  in the :file:`/etc/kibana/kibana.yml` configuration file and stored in the
  :file:`secret/` directory on the Ansible Controller. if ``absent``, the
  configuration will be removed from the generated configuration file.


Dependent configuration storage and retrieval
---------------------------------------------

The dependent configuration from other roles is stored in the :file:`secret/`
directory on the Ansible Controller (see :ref:`debops.secret` for more details) in
a JSON file, with each role configuration in a separate dictionary. The
``debops.kibana`` role reads this file when Ansible local facts indicate that
the Kibana service is installed, otherwise a new empty file is created.  This
ensures that the stale configuration is not present on a new or re-installed
host.

The YAML dictionaries from different roles are be merged with the main
configuration in the :envvar:`kibana__combined_configuration` variable that is
used to generate the final configuration. The merge order of the different
``kibana__*_configuration`` variables allows to further affect the dependent
configuration through Ansible inventory if necessary, therefore the Ansible
roles that use this method don't need to provide additional variables for this
purpose themselves.


Example role variables
----------------------

This file shows an example set of default variables included in a role that
uses the ``debops.kibana`` role as a dependency:

.. literalinclude:: examples/application-defaults.yml
   :language: yaml

Example role playbook
---------------------

This file shows an example playbook for a role that uses the
``debops.kibana`` role as a dependency:

.. literalinclude:: examples/application-playbook.yml
   :language: yaml
