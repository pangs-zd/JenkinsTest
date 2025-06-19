# Jenkins安装

## 1.1 APT安装

[Jenkins APT安装](https://pkg.jenkins.io/debian-stable/)

1. 准备工作

   开始前建议将Ubuntu的下载源替换为阿里源，用Ubuntu自带的下载源下载速度会很慢(尤其后面安装Jenkins)。
   
   ```shell
   # 更换源
   sudo sed -i 's|http://.*archive.ubuntu.com|https://mirrors.aliyun.com|g' /etc/apt/sources.list
   sudo sed -i 's|http://.*security.ubuntu.com|https://mirrors.aliyun.com|g' /etc/apt/sources.list
   
   # 更新软件包列表
   sudo apt update
   
   # 升级已安装的包
   sudo apt upgrade -y
   ```

2. 添加Jenkins密钥

   ```shell
   sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
       https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
   ```

3. 将密钥添加 Jenkins apt 存储库条目

   ```shell
   echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
       https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
       /etc/apt/sources.list.d/jenkins.list > /dev/null
   ```

4. 更新本地软件包索引

   ```shell
   sudo apt-get update
   ```

5. 配置Java环境

   ```shell
   sudo apt-get install fontconfig openjdk-17-jre
   ```

6. 安装Jenkins

   ```shell
   sudo apt-get install jenkins
   ```

7. 至此Jenkins安装完成

   ```shell
   # 查看Jenkins服务状态
   sudo systemctl status jenkins
   # 启动/关闭Jenkins服务
   sudo systemctl start jenkins
   sudo systemctl stop jenkins
   ```


# Jenkins配置

## 2.1 初始配置

1. 访问http://127.0.0.1:8080通过网页配置Jenkins

   初始配置按照指引操作即可(查看下面的图片)。

   ![image-20250523230026884](.\Picture\image-20250523230026884.png)

   ![image-20250523231951368](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250523231951368.png)

   ![image-20250525232117035](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250525232117035.png)

   ![image-20250525232158532](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250525232158532.png)

   ![image-20250525232228626](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250525232228626.png)

## 2.2 配置中文

1. 下载安装三个中文插件

   ![image-20250525142710848](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250525142710848.png)

   ![image-20250525142750552](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250525142750552.png)

2. 按下图修改配置点击保存配置

   ![image-20250525143003319](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250525143003319.png)

   至此修改中文配置完成。中文插件不是很完善，还是会有一些显示英文，不过影响不大了，将就用吧。

   **注：Jenkins重启后整个中文环境就需要重新设置，大部分原本翻译的字符还是显示英文**。重新设置方法：

   1. 将中文设置为英文并保存
   2. 重启jenkins
   3. 设置中文并保存

## 2.3 Windows访问

一般情况下只要网络是通的，Windows是可以直接访问Ubuntu下jenkins的。如果访问不了被拒绝，大概率是防火墙的问题：

1. 关闭Windows防火墙

2. 关闭Ubuntu下防火墙(若Ubuntu下防火墙不能关闭，则允许jenkins服务器端口通过防火墙)

   ```shell
   # 启用防火墙
   $ sudo ufw enable
   Firewall is active and enabled on system startup
   
   # 允许8089通过防火墙
   $ sudo ufw allow 8089/tcp
   Skipping adding existing rule
   Skipping adding existing rule (v6)
   
   # 重新加载
   $  sudo ufw reload
   Firewall reloaded
   
   # 查看
   $ sudo ufw status
   Status: active
   
   To                         Action      From
   --                         ------      ----
   8080/tcp                   ALLOW       Anywhere                  
   8080                       ALLOW       Anywhere                  
   8089/tcp                   ALLOW       Anywhere                  
   8080/tcp (v6)              ALLOW       Anywhere (v6)             
   8080 (v6)                  ALLOW       Anywhere (v6)             
   8089/tcp (v6)              ALLOW       Anywhere (v6)             
   ```

   

# Jenkins使用

## 3.1 创建拉取、编译、打包代码任务

1. 创建一个任务

   ![image-20250525233417859](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250525233417859.png)

2. 配置项目地址

   ![image-20250525233521647](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250525233521647.png)

3. 配置仓库以及访问仓库的凭证(此处我设置的凭证是访问仓库的账号和密码)

   ![image-20250525233604541](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250525233604541.png)

4. 添加凭证

   ![image-20250525233708131](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250525233708131.png)

5. 设置编译步骤，简而言之，我设置的就是通过shell脚本设置编译环境编译代码并打包

   ![image-20250525233740546](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250525233740546.png)

6. 编辑脚本

   ![image-20250527225915029](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250527225915029.png)

7. 测试任务，至此任务配置成功。

   ![image-20250525233930357](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250525233930357.png)

# 问题记录

## 4.1 插件加载

1. Git插件无法加载

   问题描述：

   Failed to load: Jenkins Git client plugin (git-client 6.1.3) - Plugin is missing: mina-sshd-api-core (2.14.0-143.v2b_362fc39576)

   解决方法：

   - 访问 [Jenkins 插件仓库](https://plugins.jenkins.io/)。
   - 搜索 `mina-sshd-api-core`，找到版本 `2.14.0-143.v2b_362fc39576`。
   - 下载对应的 `.hpi` 文件。
   - 登录Jenkins，进入 **管理Jenkins > 插件管理 > 高级**。
   - 在 **上传插件** 部分，选择下载的 `.hpi` 文件并安装。

2. 插件兼容

   问题描述：

   ![image-20250525233311636](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250525233311636.png)

   解决方法：

   只要不影响使用无需处理。

## 4.2 任务拉取Git代码

1. 找不到git工具

   问题描述：

   构建中 在工作空间 /var/lib/jenkins/workspace/JenkinsTest 中 The recommended git tool is: NONE

   解决方法：

   - 确认是否安装git相关插件，若没有安装则安装即可。

   - 若安装了git插件依然查不到，则全局指定git绝对路径。

     ![image-20250527234457975](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250527234457975.png)

     ![image-20250527234613205](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250527234613205.png)

2. 无法从配置仓库获取有效分支或提交记录

   问题描述：

   ERROR: Couldn't find any revision to build. Verify the repository and branch configuration for this job.

   解决方法：

   该错误的原因是无法从配置的仓库获取有效的分支或提交记录，可能的原因：

   1. 配置的仓库没有任何提交记录，即空仓库，任意提交一点东西即可。

   2. 配置的分支名字错误，确认以下图片位置配置是否正确。

      查看代码仓库的分支名(拉取的代码仓库目录下)：

      ```shell
      $ sudo git branch -a
      * main
        remotes/origin/HEAD -> origin/main
        remotes/origin/main
      ```

      ![image-20250527235347770](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250527235347770.png)