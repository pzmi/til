`terraform state list openstack_compute_instance_v2.some-vm-name | xargs -I{} terraform state show {} | awk '$1 == "access_ip_v4" {print $3}'`
