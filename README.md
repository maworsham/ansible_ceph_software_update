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

 * Check if cluster is healthy before continuing (via REST-API) only health_ok is good
 * Disable scrub and deep-scrub
 * Update all applications on all the hosts in the cluster
 * For every host, one by one, do the following:
   - Check if applications got updated
   - Check via reboot-hint if a reboot is necessary
   -  If applications got updated or reboot is necessary, do the following :
   -  Put host in maintenance
   -  Reboot host if necessary
   -  Check and wait via 'ceph orch host ls' if status of the host is maintance and nothing else
   -  Get host out of maintenance
   -  Check if cluster is healthy before continuing (via Rest-API) only warning about scrub and deep-scrub is allowed, but no pg's should be degraded
 * Enable scrub and deep-scrub when all hosts are done
 * Check if cluster is healthy (via Rest-API) only health_ok is good
 * Done

