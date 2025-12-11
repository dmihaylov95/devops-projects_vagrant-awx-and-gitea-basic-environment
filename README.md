# devops-projects_vagrant-awx-and-gitea-basic-environment
## DevOps Projects // Provisioning Automation, Configuration Management and Version Control // Vagrant AWX and Gitea Basic Environment

This is a Vagrantfile configuration that deploys and provisions a basic AWX and Gitea environment and 2 additional generic servers (one AlmaLinux and one Debian). The servers can then be added to the AWX manually and jobs can be configured to run on them. The servers can also be manually adapted in the configuration to be different distributions and/or different number of servers, depending on the current needs. Adding repositories to the Gitea and configuring the AWX to work with them must be done manually after deploying the environment. In future versions adding the servers to the AWX, choosing number of servers and distributions, adding repositories to the Gitea and configuring the AWX to work with them will be automated, so the entire environment will be ready to use right away after deployment.

### Instructions:
1. Make sure you have the latest version of Vagrant installed from: https://developer.hashicorp.com/vagrant/install
2. Make sure you have a working Oracle VirtualBox: https://www.virtualbox.org/wiki/Downloads (the confirguration can be adapted to work with a different virtualization software if that is needed).
3. Make sure that your VirtualBox is started.
4. Pull the repository, containing the Vagrantfile, locally.
5. Go to the directory, where the Vagrantfile lives.
6. Open a terminal from that directory.
7. Execute `vagrant up` and be ready to wait, as this takes a while (normally 15-30 minutes).
8. Once ready:
   * You should be able to access AWX at: https://192.168.99.99:8043/
   * You should be able to access Gitea at: http://192.168.99.99:8888/

Credentials for the AWX superuser:
- Username: awx
- Password: awx

Credentials for the Gitea admin user:
- Username: gitea
- Password: giteagitea

* Credentials can also be changed from the Vagrantfile.

Once you need to clear-up the environment, from the same terminal, opened from the directory, where the Vagrantfile lives, execute `vagrant destroy --force`.
