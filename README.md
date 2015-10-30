DEV ENV
=======

Windows
-------

    install VirtualBox
    install Vagrant
    (local host) git config --global core.autocrlf false
    (local host) git clone
    (local host) vagrant plugin install vagrant-winnfsd
    (local host) vagrant up
    (local host) $(deploy/vagrant-shell-init)

OSX, Linux
---

    install VirtualBox
    install Vagrant
    (local host) git clone
    (local host) vagrant up
    (local host) $(deploy/vagrant-shell-init)