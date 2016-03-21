# -*- mode: ruby -*-
# vi: set ft=ruby :

$install = <<SCRIPT
echo "Start Wendelin Installation"
wget http://deploy.nexedi.cn/wendelin-standalone
bash wendelin-standalone
echo "End Wendelin Installation: execute chown -R slapsoft:slapsoft /opt/slapgrid command"
echo "Monitoring progress of Wendelin installation: execute tail -f /opt/slapos/log/*.log command"
echo "Accessing Wendelin instance: execute erp5-show -s command"
SCRIPT

nodes_config = (JSON.parse(File.read("wendelin-nodes.json")))['nodes']

Vagrant.configure("2") do |config|

  # Global parameters for the Grid'5000 provider
  config.vm.provider "grid5000" do |g5k|

    # Grid'5000 authentification parameters for Ruby-Cute
    # see http://www.rubydoc.info/github/ruby-cute/ruby-cute/master/Cute%2FG5K%2FAPI%3Ainitialize
    # default: use ~/.grid5000_api.yml
    g5k.cute_parameters = { :username => 'wsaad', :password => '09072011@walid' }

    # Site to reserve on (default: nancy)
    #g5k.site = rennes

    # OAR queue to use (default: default)
    #g5k.queue = 'default'

    # OAR properties to use (default: none)
    #g5k.properties = "cluster='griffon'"

    # Walltime (default: reserve until the next end of day (18:55))
    #g5k.walltime = '01:00'

    # Environment to deploy (default: jessie-x64-min)
    #g5k.env = 'jessie-x64-base'
  end
####Create Wendelin Nodes####
nodes_config.each do |node|
    node_name   = node[0] # name of node
    node_parameters = node[1] # content of node
    site = node_parameters[':site']
    node_number = node_parameters[':node-number']
    for i in (1..node_number) do
       box_name=site + node_name + "node" + i.to_s
       config.vm.define box_name do |tb2|
       tb2.vm.box = "dummy"
       # VM-specific overrides
       tb2.vm.provider 'grid5000' do |g5k|
       #g5k.site = 'lyon'
       g5k.site = node_parameters[':site']
       g5k.env = node_parameters[':env']
       g5k.walltime = '02:00'
       #g5k.nodes = '2'
       end
       #tb2.vm.provision "shell" do |s|
       # s.inline = $install
       # s.args   = node_name + i.to_s
       #end
       #tb2.vm.provision "shell", privileged: false, inline: $install
       # Synced folders configuration, using rsync (you need to install it if you use -min or -base, see below)
       tb2.vm.synced_folder ".", "/vagrant", type: "rsync", rsync__exclude: ".git/"
    end
end
end
#######Begin provision for all node#########
##config.vm.provision "shell", privileged: false, inline: 'apt-get update && apt-get -y install sudo rsync'
  # privileged: false is no longer required
 config.vm.provision "shell", inline: $install
#######end provision for all node#########
end
