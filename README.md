# infra-ceph
Ceph-Related Solutions and Scripts

Ceph Patching Solution (original code):
* We're using Red Hat Ansible Automation Platform and RHEL 8/9 for the hosts.
* We get a mail when the Job fails.
* Deployed Ceph via Cephadm.
* Cephadm hosts are hosts with the following labels and services: mon,_admin,mgr,alertmanager,prometheus,grafana.
* The load balancer is broken in our setting; there are issues with TLS 1.3. So we have a little workaround to call the cephadm/mgr nodes directly. Waiting for 18.2.3 to enable TLS 1.2 on the mgr.
* Roles is empty in this export.

This Playbook works for us, make adjustments to get it working for yourself :-)

---

The ceph-patching playbook is an Ansible playbook

 1. Check if cluster is healthy before continuing (via REST-API) only health_ok is good
 2. Disable scrub and deep-scrub
 3. Update all applications on all the hosts in the cluster
 4. For every host, one by one, do the following:
 4a. Check if applications got updated
 4b. Check via reboot-hint if a reboot is necessary
 4c. If applications got updated or reboot is necessary, do the following :
 4c1. Put host in maintenance
 4c2. Reboot host if necessary
 4c3. Check and wait via 'ceph orch host ls' if status of the host is maintance and nothing else
 4c4. Get host out of maintenance
 4d. Check if cluster is healthy before continuing (via Rest-API) only warning about scrub and deep-scrub is allowed, but no pg's should be degraded
 5. Enable scrub and deep-scrub when all hosts are done
 6. Check if cluster is healthy (via Rest-API) only health_ok is good
 7. Done

