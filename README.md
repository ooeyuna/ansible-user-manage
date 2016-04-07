# Ansible用户管理

- 任务说明
    
    - 用户创建,用户同名私有组创建,生成home目录,加入sudo组,更改登陆密码,指定默认shell为bash,tags:adduser
      
    - 将`{{username}}`的公钥同步到`{{username}}`用户下,公钥地址为ansible项目`roles/user/files/{{username}}`,[地址](roles/user/files/),tags:sshkey
      
    - 当remove参数存在时,执行删除用户操作,注意该操作会删除该用户home下所有数据,不可逆操作!!!,tags:removeuser
	
	- 不允许修改root

- 变量

    - `hostname` 需要操作的服务器组名
    - `username` 用户名,不允许为root
    - `remove` 是否删除该用户,不传则不执行删除操作
	- `group` 添加用户到某个组,可指定多个,逗号分隔


- 使用说明

    以test用户,local服务器组为例

    - 创建用户/修改登陆密码,同时发布公钥

        - 用mkpassword生成密码并记下该加密串

            ```
            mkpasswd --method=SHA-512
            ```
        - 将加密串写入到`roles/user/files/passwd/{{ username }}`,这里用的是test用户所以是写到`roles/user/file/passwd/test`
			
        - 获得公钥,将公钥写入`roles/user/files/{{ username }}`,这里用的是test用户所以是写到`roles/user/files/test`中,该文件格式等同于authorized_keys

        - 执行脚本创建用户/修改密码,并发布公钥

            ```
            ansible-playbook -v -i staging/hosts user.yml -e 'hostname=local username=test group=sudo'
            ```



    - 创建用户/修改密码(不发布公钥)

        - 用mkpassword生成密码并记下该加密串

            ```
            mkpasswd --method=SHA-512
            ```
            
        - 执行脚本创建用户/修改密码(其实就是指定tag为adduser)

            ```
            ansible-playbook -v -i staging/hosts user.yml -t adduser -e 'hostname=local username=test'
            ```
            
            

    - 发布公钥

        - 获得公钥,将公钥写入`roles/user/files/{{ username }}`,这里用的是test用户所以是写到`roles/user/files/test`中,该文件格式等同于authorized_keys

        - 执行脚本同步公钥

            ```
            ansible-playbook -v -i staging/hosts user.yml -e 'hostname=local username=test'
            ```
            
            

    - 删除用户

        - 执行脚本删除用户(remove参数只要存在就是删除,无论value是什么)

            ```
            ansible-playbook -v -i staging/hosts user.yml -e 'hostname=local username=test remove=true'
            ```

