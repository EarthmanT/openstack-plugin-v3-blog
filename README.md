# Openstack-Plugin-v3-Blog-Post

Our new major version of the Openstack plugin is out. Many significant changes have been made. There is limited backward compatibility and users really should update their blueprints to use it. 

First of all, let's address the question, why a new version of the Openstack Plugin?

The Openstack plugin was our first plugin for Cloudify. It was developed before there was an Official Python Openstack SDK. (It is based on the Openstack Python CLI packages). Also, in the five years since we wrote the plugin, we have acquired a lot of experience in writing other plugins and developed our best practices. We want to bring that quality experience back to our Openstack users.

**This means that Openstack Plugin v2 is now deprecated. It will continued to be maintained for bug fixes until August 2020.**

So what are these great improvements?

On the outside, you will notice these changes:

New node and relationship type naming convention: "cloudify.nodes.openstack.ResourceName" and "cloudify.relationships.openstack.relationship_type". For example `cloudify.nodes.Openstack.Server` and `cloudify.relationships.openstack.server_connected_to_port`.

New Common properties. All resources have the following properties. Examples:
  - `client_config`: The standard property for all plugins for client connections.
  - `resource_config`: The standard property for all plugins for API parameters.

We still support `use_external_resource`, however it will search for an existing resource according to the `resource_config: id` or `resource_config: name` property instead of `resource_id`.

New properties. Anytime, when we want to have Cloudify behave in a unique way, we use node properties to guide that property. Examples:
  - `use_public_ip`: If a server has a public IP, use the public IP as its default IP instead of the private IPv4 address.
  - `use_ipv6_ip`: If the server has an IPv6 IP, use the IPv6 IP as its default IP instead of the private IPv4 address.

In addition, we have added DSL level validation for most `resource_config` properties, for example: Openstack Volume resource_config will check that your size is an integer and your description is a string:

```python
  example-volume:
    type: cloudify.nodes.openstack.Volume
    properties:
      client_config: *client_config
      use_external_resource: { get_input: volume1_use_existing }
      resource_config:
        name: myvol
        description: My vol.
        size: 24
```

On the inside, you will notice these changes:

1. Dependence on the official Python OpenstackSDK.
1. Removal of all CLI Python Client packages, for example: `neutron_plugin`, `cinder_plugin`, `nova_plugin`, etc.
1. Division of Python Packages into `openstack_sdk` (interfaces for the SDK) and `openstack_plugin` (Cloudify-specific code).
1. One unified decorator `with_openstack_resource` that describes the common behavior for all resources.
1. Ability to pass a unique `existing_resource_handler` for special handling of external resources.

We will continue to maintain it and provide bug fixes for the next year and a half. However, you should do your best to move to the v3 plugin ASAP. In order to provide a measure of backward compatibility, we have made added a special feature, which will only be supported as long as v2 will be supported. This is a special `compat.yaml` file and a `compat` decorator in the Python code. In order to use existing node types that do not override any lifecycle interfaces, users can import the `compat.yaml` and use old node types with the new plugin.
