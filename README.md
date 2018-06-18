emacs-git
=========

Ansible role to install Emacs from official Git repository in
centOS 6.x machines.


Requirements
------------

None

Role Variables
--------------

emacs_git_repository: The official Emacs repository.
emacs_git_deploy_to: Directory where to build Emacs.
emacs_git_version: The Emacs version to install; A branch or tag name.
emacs_git_autoconf_min_version: The minimum autoconf version to build Emacs.

emacs_git_autoconf_filename:
emacs_git_autoconf_deploy_dir: Directory where to build autoconf.
emacs_git_git_v2_url: URL for git v2 RPM package for CentOS.
emacs_git_configure_opts: Build options passed to 'configure' to build Emacs. 

Dependencies
------------

ncurses-devel
libselinux-python
gnutls-devel
autoconf v>= 2.65

Example Playbook
----------------

Example using 3 Vagrant VM: control, server01, server02.
See the directory 'example'.

- Copy 'example' directory into a directory where you have write permissions
  (for instance '/tmp'):
$ cp -r example /tmp && cd /tmp/example  

- Provision the 3 Vagrant VM machines, control, server01, server02:
$ for host in control server01 server02
  do
     (cd $host && vagrant up)
  done

- Stablish a ssh connection with the control machine:
$ (cd control && vagrant ssh)

- Update /etc/hosts in the control machine with the servers IP's.
$ cat /etc/hosts > foo
$ echo "192.168.1.171 server01" >> foo
$ echo "192.168.1.172 server02" >> foo
$ sudo mv foo /etc/hosts

- from control machine, create a private ssh key
  Do not use any password, just press enter:
$ ssh-keygen -t rsa

-  Distribute the public key to the servers (server01, server02)
   When you are prompted for a password, use 'vagrant':
$ for host in server01 server02
  do
    ssh vagrant@$host mkdir -p .ssh
  done

$ for host in server01 server02
  do
    cat ~/.ssh/id_rsa.pub|ssh vagrant@$host 'cat >> .ssh/authorized_keys'
  done

- Install ansible in the control machine:
$ wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
$ sudo yum install -y epel-release-6-8.noarch.rpm
$ sudo yum repolist
$ sudo yum update -y
$ sudo yum install -y ansible

- Install in control machine the official nginx ansible role:
$ ansible-galaxy install nginxinc.nginx --roles-path=$HOME/.ansible/roles

- Make a soft link to the control roles directory:
$ ln -s /home/vagrant/.ansible/roles /home/vagrant/ansiblework/playbooks/roles

- Change directory and run the playbook:
$ cd /home/vagrant/ansiblework
$ ansible-playbook playbooks/main.yml

- Test that Emacs has ben installed in server01 and server02:
$ curl server01
$ curl server02

[You must see a message saying that Emacs has been installed in that machine]


License
-------

GPLv3

Author Information
------------------

[Tino Calancha](https://github.com/calancha) 2018.
