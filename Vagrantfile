Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/impish64"
  
  	config.vm.define "dockerhost" do |dockerhost|
		dockerhost.vm.hostname = "dockerhost"
		dockerhost.vm.network "forwarded_port", guest: 5000, host: 5000
		dockerhost.vm.network "forwarded_port", guest: 4141, host: 4141
		dockerhost.vm.network "forwarded_port", guest: 5151, host: 5151
		#ansibleController.vm.network "private_network", ip: "10.9.8.10"
	end
end