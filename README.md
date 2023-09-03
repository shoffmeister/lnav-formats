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

Resource-based queries address a specific resource, i.e. either a `Pod` directly
or a CRD type that emits logs, e.g. `Deployment` or `StatefulSet`

Connect to one specific `Pod` with all its containers:

```shell
lnav kubectl://resource/my-pod
```

```shell
lnav oc://resource/my-pod
```

Connect to a specific `Deployment`:

```shell
lnav kubectl://resource/deployment/my-deployment
```

```shell
lnav oc://resource/deployment/my-deployment
```

### Selector-based queries

Run a selector query and connect to the logs of all pods (and containers)
found.

```shell
lnav kubectl://selector/app.kubernetes.io/managed-by=Helm
```

```shell
lnav oc://selector/app.kubernetes.io/managed-by=Helm
```

### Tuning through parameters

URL query parameters can be used to tune query. Beware
of using the ampersand (&) character when using more than
one query parameter - shells may interpret this, if not
quoted properly, e.g.

```shell
lnav 'kubectl://selector/app=mine?param1=hello&param2=world'
```

#### --max-log-requests support

Use the `max-log-requests` query parameter to extend
the maximum number of logs that can be opened at the
same time:

```shell
lnav 'kubectl://selector/key=value?max-log-requests=20'
```

#### --namespace support

By adding the `namespace` query parameter, the default namespace
can be overriden:

```shell
lnav 'kubectl://selector/key=value?namespace=kube-system'
```

This is particularly useful for cross-namespace log analysis,
e.g.

```shell
lnav \
  'kubectl://selector/key=value?namespace=namespace-one' \
  'kubectl://selector/key=value?namespace=namespace-two'
```
