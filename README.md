# Openshift under Kubernetes


[![](https://img.shields.io/pypi/dm/openshift-under-kubernetes.svg)](https://pypi.python.org/pypi/openshift-under-kubernetes/)
[![](https://img.shields.io/pypi/v/openshift-under-kubernetes.svg)](https://pypi.python.org/pypi/openshift-under-kubernetes/)
[![](https://travis-ci.org/paralin/openshift-under-kubernetes.svg)](https://travis-ci.org/paralin/openshift-under-kubernetes/)

Deploys OpenShift Origin to an existing Kubernetes cluster. This is arguably the most ideal way to deploy OpenShift, for a few reasons:

 - OpenShift Origin is a layer on top of Kubernetes
 - Kubernetes is extremely flexible in the ways it can be deployed, with kube-up
 - Allowing Kubernetes and OpenShift to be independently updated is ideal for feature iteration and stability
 - This is much easier than dealing with Ansible under OpenShift v3.

Requirements
============

This system requires the following things:

 - A running working Kubernetes cluster.
 - A kubeconfig for this cluster with long-term API authentication details at maximum administrative level.
 - A Persistent Volume with at least 2Gi of space.

# Deploy Process

This is the process this system uses to deploy OpenShift:

- Fetch the ServiceAccount public key by creating a temporary namespace and pod and fetching via the pod logs.
- Create the `openshift-origin` namespace.
- Create a secret containing the kubeconfig with administrative access
- Create the "openshift" service and grab the internal and external addresses.
- Run openshift with the "--write-config" option to generate the initial default config.
- Create a `PersistentVolume` inside this namespace for storage of OpenShift data.
- Create a `PersistentVolumeClaim` to claim this storage.
- Create the single-node "etcd" cluster to store the openshift data.
- Create the OpenShift replication controller.

# Usage

To install it:

    $ pip install openshift-under-kubernetes

To use it:

    $ openshift-under-kubernetes --help

This will output:

    Usage: openshift_under_kubernetes [OPTIONS] COMMAND [ARGS]...

    Options:
      --config PATH             kube config path
      --context TEXT            kube context
      --openshift-version TEXT  force openshift version (default 1.1.3)
      --secure / --no-secure    enables https checking
      -y                        auto-answer for all confirmations
      --help                    Show this message and exit.

    Commands:
      deploy      Deploy OpenShift to the cluster.
      editconfig  Interactively edits master-config.yaml
      getconfig   Writes the entire openshift config to a...
      info        Show cluster information
      undeploy    Removes OpenShift from the cluster.

You can get started by running `openshift-under-kubernetes info` to check that the tool can properly communicate with your cluster. Then, use "deploy" to publish a new openshift cluster.
