Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-20.04"
  config.vm.provider :libvirt do |lv, override|
    override.vm.box = "generic/ubuntu2004"
    override.vm.allowed_synced_folder_types = [:libvirt, :nfs]
    override.vm.synced_folder ".", "/vagrant", nfs_udp: false
    override.vm.provision 'fix-dns', type: "shell", run: 'never' do |script|
      script.inline = <<-SHELL
        sudo sed -i -e '/nameservers:/d' -e '/addresses:/d' /etc/netplan/01-netcfg.yaml
        sudo netplan generate && sudo netplan apply
        sudo sed -i 's/^[[:alpha:]]/#&/' /etc/systemd/resolved.conf
        sudo systemctl restart systemd-resolved.service
      SHELL
    end
  end
  config.vm.provision 'base', type: 'ansible_local' do |ansible|
    ansible.playbook = 'prov-playbook.yml'
    ansible.galaxy_role_file = "requirements.yml"
    ansible.galaxy_command = "ansible-galaxy collection install -r %{role_file} --force && ansible-galaxy role install -r %{role_file} --force"
    ansible.pip_install_cmd = "curl -fsSL https://git.io/Jt9mk | sudo bash -xs"
    ansible.version = 'latest'
    ansible.install_mode = 'pip'
    ansible.extra_vars = {
      ansible_python_interpreter: "/usr/bin/env python3"
    }
  end
end