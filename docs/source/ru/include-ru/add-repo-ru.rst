.. _add_repo_ru:

.. list-table::
   :widths: 10 30
   :header-rows: 1

   * - Операционная система
     - Команда
   * - Debian 7.x (wheezy)
     - .. code-block:: command

          apt-key adv --keyserver keys.gnupg.net --recv-keys 72B865FD

       .. code-block:: command

          echo 'deb http://repo.wallarm.com/debian/wallarm-node wheezy/' >/etc/apt/sources.list.d/wallarm.list

       .. code-block:: command

          apt-get update
          
   * - Debian 8.x (jessie)
     - :command:`apt-key adv --keyserver keys.gnupg.net --recv-keys 72B865FD`
       :command:`echo 'deb http://repo.wallarm.com/debian/wallarm-node jessie/' >/etc/apt/sources.list.d/wallarm.list`
       :command:`apt-get update`
   * - Ubuntu 14.04 LTS (trusty)
     - :command:`apt-key adv --keyserver keys.gnupg.net --recv-keys 72B865FD`
       :command:`echo 'deb http://repo.wallarm.com/ubuntu/wallarm-node trusty/' >/etc/apt/sources.list.d/wallarm.list`
       :command:`apt-get update`
   * - Ubuntu 16.04 LTS (xenial)
     - :command:`apt-key adv --keyserver keys.gnupg.net --recv-keys 72B865FD`
       :command:`echo 'deb http://repo.wallarm.com/ubuntu/wallarm-node xenial/' >/etc/apt/sources.list.d/wallarm.list`
       :command:`apt-get update`
   * - CentOS 6.x
     - :command:`yum install --enablerepo=extras -y epel-release centos-release-SCL`
       :command:`rpm -i https://repo.wallarm.com/centos/wallarm-node/6/x86_64/Packages/wallarm-node-repo-1-2.el6.noarch.rpm`
   * - CentOS 7.x
     - :command:`yum install -y epel-release`
       :command:`rpm -i https://repo.wallarm.com/centos/wallarm-node/7/x86_64/Packages/wallarm-node-repo-1-2.el7.centos.noarch.rpm`
   