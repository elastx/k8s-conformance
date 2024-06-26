# Conformance tests for Diamanti Kubernetes Engine

## Install Diamanti Ultima v3.8.x (based on Kubernetes v1.28.x) and create a cluster

Use regular dctl command to create a cluster (consult Exploring the Diamanti CLI Guide for details) and then create a default network for that cluster.  If you are using Diamanti Ultima Accelerator hardware, for your default network set host-network flag as enabled.

Diamanti 3 nodes cluster:

```
[diamanti@appserv9 ~]$ dctl cluster status
Name           	: devtb6
UUID           	: d14239bd-8ff7-11ee-aae2-2c600c82ec72
State          	: Created
Version        	: 3.8.0 (0)
Etcd State     	: Healthy
Virtual IP     	: 172.16.19.23
Storage VLAN   	: 430
Pod DNS Domain	: cluster.local

NAME        NODE-STATUS   K8S-STATUS   ROLE      MILLICORES   MEMORY          STORAGE    IOPS      VNICS     BANDWIDTH   SCTRLS
                                                                                                                         LOCAL, REMOTE   
appserv10   Good          Good         master    100/32000    1.07GiB/64GiB   0/3.05TB   0/500K    5/63      0/18G       0/64, 0/64
appserv11   Good          Good         master    200/32000    1.26GiB/64GiB   0/3.05TB   0/500K    6/63      0/18G       0/64, 0/64
appserv9    Good          Good         master*   100/32000    1.07GiB/64GiB   0/3.05TB   0/500K    6/63      0/18G       0/64, 0/64
```

## Run Conformance Test

The standard tool for running these tests is
[Sonobuoy](https://github.com/heptio/sonobuoy).  Sonobuoy is
regularly built and kept up to date to execute against all
currently supported versions of kubernetes, and can be obtained [here](https://github.com/heptio/sonobuoy/releases).

Download the CLI by running:

```

$ wget https://github.com/vmware-tanzu/sonobuoy/releases/download/v0.56.16/sonobuoy_0.56.16_linux_amd64.tar.gz

```

Deploy a Sonobuoy pod to your cluster with:

```
$ sonobuoy run --mode=certified-conformance
```

View actively running pods:

```
$ sonobuoy status
```


To inspect the logs:

```
$ sonobuoy logs
```

Once `sonobuoy status` shows the run as `completed`, copy the output directory from the main Sonobuoy pod to
a local directory:

```
$ sonobuoy retrieve
```

This copies a single `.tar.gz` snapshot from the Sonobuoy pod into your local
`.` directory. Extract the contents into `./results` with:

```
mkdir ./results; tar xzf *.tar.gz -C ./results
```

**NOTE:** The two files required for submission are located in the tarball under **plugins/e2e/results/{e2e.log,junit.xml}**.

To clean up Kubernetes objects created by Sonobuoy, run:

```
sonobuoy delete
```


