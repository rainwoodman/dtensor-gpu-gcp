# dtensor-gpu-gcp

This project contains an example of using multi-client DTensor on GCP with a
cluster of GPUs.

- deploy/gce-cluster: First build a cluster of 4 GCE GPU VMs, then run 1 dtensor
  client per VM.

- deploy/gce-node: First build a single GCE GPU VM with 4 gpus, then run 1 client
  per GPU on the VM. For each dtensor client, the unwanted GPUs are hidden via
  `CUDA_VISIBILE_GPU`.

- dtensor-client.py: the dtensor application. The simple example creates a
  distributed tensor (a DTensor), and performs a collective reduction.

In each `deploy/*` directory:

- bootstrap.sh: Starts the cluster / node.

- launch.sh: The application launcher for this cluster. It configures the
  DTensor environment variables before launching the command provided in 
  the command-line as dtensor clients.
  This script (and dependency) are broadcast to the VMs and shall be run from
  the VMs (e.g. via cluster-run.sh).

- cluster-run.sh: (produced by bootstrap.sh) runs the provided command
  on all VMs in the cluster.

- cluster-bcast.sh: (produced by bootstrap.sh) copies the file to all VMs.

- cluster-delete.sh: (produced by bootstrap.sh) deletes all VMs
  in the cluster.

Steps to run the application, e..g using the GCE cluster example:

```
$ gcloud auth login ...

$ git clone ...
$ cd dtensor-gpu-gcp

# Run from the cluster deployment:
$ cd deploy/cluster
$ bash bootstrap.sh
$ bash cluster-run.sh "bash launch.sh python dtensor-gpu-gcp/dtensor-client.py"
$ bash cluster-delete.sh
```
