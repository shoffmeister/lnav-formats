# Custom URL schemes for kubectl and oc log analysis with lnav

This repository contains a set of custom URL schemes accepted by <https://lnav.org/> enabling convenient access to Kubernetes (`kubectl`) and OpenShift (`oc`) log data.

**Status: ALPHA + EXPERIMENTAL**

Use at your own risk, feedback welcome.

## System requirements

* an installation of `lnav` which supports custom URL schemes. lnav 0.11.2 does not contain this functionality, it was only present in CI builds at the time of this writing
* `kubectl` and/or `oc` binaries on the PATH

The implementation does not contain any operating system-specific functionality. It will therefore work wherever `lnav`, `kubectl`, and `oc` are available.

## Installation and updating

Install the custom schemes by executing

```shell
lnav -i https://github.com/shoffmeister/lnav-formats.git
```

To update, execute

```shell
lnav -u https://github.com/shoffmeister/lnav-formats.git
```

## Usage

The implementation drives the "logs" command exposed by the
respective container platform's CLI tools, see

* `kubectl logs` at <https://kubernetes.io/docs/reference/kubectl/>
* `oc logs` at <https://docs.openshift.com/container-platform/4.13/cli_reference/openshift_cli/developer-cli-commands.html>

### Resource-based queries

Connect to one specific `Pod` with all its containers:

```shell
lnav kubectl://my-pod
```

```shell
lnav oc://my-pod
```

Connect to a specific `Deployment`:

```shell
lnav kubectl://deployment/my-deployment
```

```shell
lnav oc://deployment/my-deployment
```

Instead of using a `Deployment` any type of CRD supporting
logs can be used, for instance a `StatefulSet`.

### Selector-based queries

Run a selector query and connect to all pods (and containers)
found. As such a query may return a large number of log sources,
the implementation proactively configures a significantly increased
value for `--max-log-requests` - buyer beware.

```shell
lnav kubectl-select://app.kubernetes.io/managed-by=Helm
```

```shell
lnav oc-select://app.kubernetes.io/managed-by=Helm
```
