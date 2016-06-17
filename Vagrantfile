# -*- mode: ruby -*-
# vi: set ft=ruby :

boxes = [
{
	:name => "ms-sql-server-testing",
	:eth1 => "192.168.50.4",
	:mem => "4096",
	:cpu => "4",
	:box => "opentable/win-2008r2-standard-amd64-nocm",
	:shells => [
		"scripts/install-dot-net.ps1",
		"scripts/install-sql-server.cmd",
		"scripts/configure-sql-port.ps1",
		"scripts/enable-rdp.ps1"
],
:files => [],
:ports_forward => [
	{
	:guest => "3389", :host => "3389"},
	{
	:guest => "1433", :host => "1433"}
]
}
]

if ! File.exists?('./SQLEXPRWT_x64_ENU.exe')
	puts 'SQL Server installer could not be found!'
	puts "Please run:\n  wget http://download.microsoft.com/download/0/4/B/04BE03CD-EAF3-4797-9D8D-2E08E316C998/SQLEXPRWT_x64_ENU.exe"
	system ("wget http://download.microsoft.com/download/0/4/B/04BE03CD-EAF3-4797-9D8D-2E08E316C998/SQLEXPRWT_x64_ENU.exe")
end


Vagrant.configure(2) do |config|
	boxes.each do |opts|

		config.vm.define opts[:name] do |config|

			config.vm.provider "virtualbox" do |v, override|
				override.vm.box = opts[:box]
			end
			config.vm.hostname = opts[:name]

			config.vm.provider "virtualbox" do |v|
				v.customize ["modifyvm", :id, "--memory", opts[:mem]]
				v.customize ["modifyvm", :id, "--cpus", opts[:cpu]]
			end

			config.vm.network :private_network, ip: opts[:eth1]
			opts[:ports_forward].each do |item|
				config.vm.network "forwarded_port", guest: item[:guest], host: item[:host]
			end

			opts[:files].each do |fileitem|
				config.vm.provision :file do |file|
					file.source = fileitem[:src]
					file.destination = fileitem[:dst]
				end
			end

			opts[:shells].each do |item|
				config.vm.provision :shell, path: item
			end
		end
	end
end
