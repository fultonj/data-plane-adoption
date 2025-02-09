# Adoption of other services

This part of the guide adopts the remaining services that don't have a
specific guide of their own. It is likely that as adoption gets
developed further, services will be removed from here and put into
their own guides (e.g. like
[Glance](https://github.com/openstack-k8s-operators/data-plane-adoption/blob/main/glance_adoption.md)).

## Prerequisites

* Previous Adoption steps completed.

## Variables

(There are no shell variables necessary currently.)

## Pre-checks

## Procedure - Adoption of other services


* Deploy the rest of the control plane services:

  ```
  oc patch openstackcontrolplane openstack --type=merge --patch '
  spec:
    # cinder:
    #   enabled: true
    #   template:
    #     cinderAPI:
    #       replicas: 1
    #       containerImage: quay.io/podified-antelope-centos9/openstack-cinder-api:current-podified
    #     cinderScheduler:
    #       replicas: 1
    #       containerImage: quay.io/podified-antelope-centos9/openstack-cinder-scheduler:current-podified
    #     cinderBackup:
    #       replicas: 1
    #       containerImage: quay.io/podified-antelope-centos9/openstack-cinder-backup:current-podified
    #     cinderVolumes:
    #       volume1:
    #         containerImage: quay.io/podified-antelope-centos9/openstack-cinder-volume:current-podified
    #         replicas: 1

    ovn:
      enabled: true
      template:
        ovnDBCluster:
          ovndbcluster-nb:
            replicas: 1
            containerImage: quay.io/podified-antelope-centos9/openstack-ovn-nb-db-server:current-podified
            dbType: NB
            storageRequest: 10G
          ovndbcluster-sb:
            replicas: 1
            containerImage: quay.io/podified-antelope-centos9/openstack-ovn-sb-db-server:current-podified
            dbType: SB
            storageRequest: 10G
        ovnNorthd:
          replicas: 1
          containerImage: quay.io/podified-antelope-centos9/openstack-ovn-northd:current-podified

    ovs:
      enabled: true
      template:
        ovsContainerImage: "quay.io/skaplons/ovs:latest"
        ovnContainerImage: "quay.io/podified-antelope-centos9/openstack-ovn-controller:current-podified"
        external-ids:
          system-id: "random"
          ovn-bridge: "br-int"
          ovn-encap-type: "geneve"

    neutron:
      enabled: true
      template:
        databaseInstance: openstack
        containerImage: quay.io/podified-antelope-centos9/openstack-neutron-server:current-podified
        secret: osp-secret

    # nova:
    #   enabled: true
    #   template:
    #     secret: osp-secret
  '
  ```


## Post-checks

* See that service endpoints are defined:

  ```
  export OS_CLIENT_CONFIG_FILE=clouds-adopted.yaml
  export OS_CLOUD=adopted

  openstack endpoint list
  ```
