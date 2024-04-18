# tempo_on_k3s_vagrant_libvirt_ansible

Vagrant-libvirt setup that creates a VM with a [k3s](k3s.io) Kubernetes cluster.
In the cluster, [Grafana Tempo](https://grafana.com/docs/tempo/latest/) is
installed, together with Grafana (the graphical dashboard).

The deployment uses the `tempo` helm chart, that installs Tempo in `monolithic
(single binary) mode`.


Default OS is openSUSE Leap 15.5, but that can be changed in the Vagrantfile.
Please be aware, that this might break the Ansible provisioning.

## Sending traces using telemetrygen

In this branch the OpenTelemetry-Collector is exposed via an ingress, so you can
use
[telemetrygen](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/cmd/telemetrygen)
from outside the cluster to create traces like so:

```bash
telemetrygen traces \
    --otlp-http --otlp-insecure --duration 5s \
    --otlp-endpoint opentelemetry-collector.192.0.2.13.sslip.io:80
```

## Vagrant

1. You need `vagrant`, obviously. And `git`. And Ansible...
1. Fetch the box, per default this is `opensuse/Leap-15.5.x86_64`, using
   `vagrant box add opensuse/Leap-15.5.x86_64`.
1. Make sure the git submodules are fully working by issuing
   `git submodule init && git submodule update`
1. Run `vagrant up`
1. Open the Grafana URL that Ansible printed out at the end of the provisioning.
   Use the admin password that Ansible also printed out.
1. Go to `Explore` on the left-hand side menu and select the Tempo data source
   (if it has not already been selected). Change the `Query Type` to `Search`
   and you should already see traces in the table at the bottom of the page.
1. Party!

## Disabling the Ansible provisioning

In case you do not want Ansible to install k3s and the Kubernetes workloads
(because you want to install it yourself), just comment out the following lines
in the `Vagrantfile`:

```
    node.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "ansible/playbook-vagrant.yml"
    end # node.vm.provision
```

## Cleaning up

The VMs can be torn down after playing around using `vagrant destroy`. This will
also remove the kubeconfig file `ansible/k3s-kubeconfig`.
