# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/trusty64"

  # config.vm.network "forwarded_port", guest: 80, host: 8080
  # config.vm.network "private_network", ip: "192.168.33.10"
  
  #config.vm.synced_folder ".", "/vagrant", type: "rsync"

  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--memory", "2048"]
  end

  config.vm.provision "shell", inline: <<-EOT
    # for autogen.sh in flare
    sudo apt-get install -y build-essential automake autoconf libtool 
    # for tokyo-cabinet
    sudo apt-get install -y zlib1g-dev
    sudo apt-get install -y libbz2-dev
    # for configure in flare
    sudo apt-get install -y libhashkit-dev uuid-dev
    # for boost
    sudo apt-get install -y python-dev
    # for test
    sudo apt-get install cutter-testing-framework

    # for development
    sudo apt-get install git
    sudo apt-get install devscripts
    sudo apt-get build-dep libtokyocabinet-dev libboost-program-options-dev libboost-regex-dev libboost-serialization-dev libkyotocabinet-dev
    
    mkdir tmp
    chmod 777 tmp
    cd tmp

    # install tokyocabinet
    curl -O http://fallabs.com/tokyocabinet/tokyocabinet-1.4.48.tar.gz
    tar zxvf tokyocabinet-1.4.48.tar.gz
    cd tokyocabinet-1.4.48
    ./configure --prefix=/usr/local/tokyocabinet
    make
    sudo make install

    # install boost
    curl -O -L http://sourceforge.net/projects/boost/files/boost/1.55.0/boost_1_55_0.tar.gz
    tar zxvf boost_1_55_0.tar.gz
    cd boost_1_55_0
    ./bootstrap.sh
    sudo ./b2 install -j2 --prefix=/usr/local/boost

    sudo echo "/usr/local/boost/lib\n/usr/local/tokyocabinet/lib" > /etc/ld.so.conf.d/flare.conf
    sudo ldconfig -v

    # build if flare exists
    if [ -d /vagrant/flare ]; then
        cd /vagrant/flare
        ./autogen.sh
        ./configure --with-boost=/usr/local/boost --with-tokyocabinet=/usr/local/tokyocabinet
        make
    fi
  EOT
end
