# vagrant-dind

Docker image build files, to be used with Vagrant's Docker provider, that
allows docker-in-docker use.  I.e. Vagrant can be configured with
docker as provider and provisioner as well :-)

After all this is a mesh up of docker images `jesselang/debian-vagrant` and
`jpetazzo/dind`.

## Example Vagrantfile

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

# Use docker provider by default (i.e. without specifying --provider on command line)
ENV['VAGRANT_DEFAULT_PROVIDER'] = 'docker'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.provider "docker" do |d|
    d.image = "stesie/vagrant-dind:latest"
    d.privileged = true  # Required for "docker in docker"
    d.has_ssh = true     # Required for provisioning and 'vagrant ssh' to work.
  end

  config.vm.network "forwarded_port", guest: 8080, host: 8080

  config.vm.provision "docker" do |d|
    d.pull_images "mysql:latest"
    d.run "mysql", args: "-e MYSQL_ROOT_PASSWORD=insecure", image: "mysql:latest"
  end
end
```
