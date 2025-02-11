# Dostainer - Kubernetes Resource Exhaustion PoC Container

This container contains scripts to demonstrate resource exhaustion from within a Kubernetes clusters:
* Allocate all remaining RAM
* Allocate all remaining disk space
* Fork bomb all the things! (on courtesy of [Chris](https://www.twitter.com/brompwnie) who also demonstrated that in one of our last [presentations](https://github.com/heroku/bheu19-attacking-cloud-builds))
* inode exhaustion

Those exhaustion attacks work in many K8s environments as default settings do not include proper resource limits - and each of those can/does render one node useless (which will spread through the cluster when pods from failed nodes get rescheduled).

Hardening measures are added to [dosploy.yaml](dosploy.yaml) and listed below:
* [Memory Restrictions](https://kubernetes.io/docs/tasks/configure-pod-container/assign-memory-resource/#specify-a-memory-request-and-a-memory-limit)
* [Ephemeral Storage Limits](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#local-ephemeral-storage)
  * Keep in mind that storage limits may not apply to other volumes attached to a pod.
* [Pod PID Limit](https://kubernetes.io/docs/concepts/policy/pid-limiting/#pod-pid-limits)
  * The PID limit is configured on the `kubelet` level and applies to all pods, i.e. you cannot configure individual PID limits per pod. 
* inode exhaustion: I am not aware of a Kubernetes-native way of restricting this exhaustion attack. There are ways to use loopback mounts per container and the like but it seems like a lot of operational effort. 