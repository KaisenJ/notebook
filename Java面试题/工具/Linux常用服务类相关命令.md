#### Linux常用服务类相关命令

##### service(centos6)

- 注册在系统中的标准化程序

- 有方便统一的管理方式（常用的方法）

  ```shell
  service 服务名 start --启动服务
  service 服务名 stop --停止服务
  service 服务名 restart --重启服务
  service 服务名 reload --重载服务
  service 服务名 status --服务状态
  ```

- 查看服务的方法   /etc/init.d/服务名

- 通过chkconfig 命令设置自启动

  ```shell
  chkconfig --list | grep 服务名 --查看服务
  chkconfig --level 5 服务名 on (on是启动，off是关闭) --服务自启
  ```

##### systemctl(centos 7)

- 注册在系统中的标准化程序

- 常用的方法

  ```shell
  systemctl start 服务名(xxxx.service)
  systemctl restart 服务名(xxxx.service)
  systemctl stop 服务名(xxxx.service)
  systemctl reload 服务名(xxxx.service)
  systemctl status 服务名(xxxx.service)
  ```

- 查看服务的方法 

  ```shell
  /usr/lib/systemd/system
  ```

- 查看服务的命令

  ```shell
  systemctl list-unit-files
  systemctl --type service
  ```

- 通过systemctl命令设置自启动

  ```shell
  systemctl enable service_name --自启动服务
  systemctl disable service_name --服务不自启动
  ```

