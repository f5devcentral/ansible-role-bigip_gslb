# Ansible Role: bigip_gslb

Performs steps needed to create and manage a Global Services Load Balancing (GSLB) object
on a BIG-IP DNS platform.

GSLB is also known in F5 lingo as a "Wide IP". Regardless of your terminology of choice,
this role can be used to manage different sets of GSLB configuration.

Note that the "typical" way that GSLB is deployed involves at least two serves; one running
GTM/DNS and N more running LTM (or some other supported platform). This role specifically
handles the GTM/DNS side of this configuration.

It is not necessary to have the LTM portion pre-configured, however, to make use of this
role.

## Requirements

None.

## Role Variables

Available variables are listed below. For their default values, see `defaults/main.yml`:

    provider_server: localhost
    provider_server_port: 443
    provider_user: admin
    provider_password: secret
    provider_validate_certs: no
    provider_transport: rest
    provider_timeout: 120

Establishes initial connection to your BIG-IQ. These values are substituted into
your ``provider`` module parameter.

    bigip_glsb_app_name: localhost

The name of the GSLB app being created.

    bigip_glsb_app_domain: gslb.local.com

The domain of the app being created.

    bigip_glsb_pool_lb_method: round-robin

The load balancing method to use for the application.

    bigip_glsb_record_type: a

The type of record to create.

    bigip_glsb_state: present

The state of the application. When ``present`` is used, this role will create the necessary
objects to stand up the GSLB configuration. Alternatively, ``absent`` will remove the
objects, effectively tearing down the GSLB configuration.

    bigip_glsb_pools:
      - name: "{{ app_name }}.{{ app_domain }}-pool"
        members:
          - server: test-server
            virtual_server: 10.1.2.3:443

The pools to create as part of the GSLB. By default, we provide a dummy implementation that
will likely not be relevant to your environment. It is provided though to give you a working
example of what you, yourself, will need to provide to correctly setup a GSLB configuration.

## Dependencies

* The BIG-IP must be onboarded, including common system bootstrapping setup steps.
* GTM/DNS must be pre-provisioned on the BIG-IP having this role.
* Addresses/FQDNs of downstream LTMs that you want the GSLB configuration to load balance to.
* The Datacenters and Server objects already exist on the target BIG-IP.

## Example Playbook

    - name: Deploy GSLB configuration
      hosts: gtm
      vars:
        bigip_glsb_pools:
          - name: "app1.domain-pool"
            members:
              - server: test-server1
                virtual_server: 10.1.2.3:443
      roles:
        - role: f5devcentral.bigip_gslb

## License

Apache

## Author Information

This role was created in 2018 by [Tim Rupp](https://github.com/caphrim007), with the help of
[Forrest Crenshaw](https://github.com/focrensh).
