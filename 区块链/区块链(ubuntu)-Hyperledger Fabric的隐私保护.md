前面两篇博客主要介绍了Hyperledger Fabric中的chaincode环境搭建、部署及测试，以及基于Java的SDK开发，这篇博客主要介绍一下基于Hyperledger Fabric的程序中如何实现数据隐私保护。

## 1、实验环境搭建

- 重新clonegitlab上的`workspace`文件夹，因为之前配置的环境中默认是用以前的`workspace`，该工作目录下没有`example03_java`的应用。	

  - 重新拉取而不是删除原来的`workspace`，删除原来的文件夹容易出一些解决不了的bug，建议是将新的`workspace`拷贝到另外一个目录，然后替换或添加app和chaincode文件夹中的example03
  - 完成上一步后注意**文件权限问题！**

  ```
  git clone https://gitlab.com/qubing/blockchain_lab_v2.git ~/new_workspace
  ```

- 强烈建议安装code-server对项目进行访问！

  - 安装code-server过程参见[AWS Ubuntu安装可视化操作桌面和VS Code，Code Server](https://blog.csdn.net/Linrena/article/details/106309969)
  - 博主配置的环境默认在已经安装好code-server的，code-server的端口号以及登录密码自己去查看
  - 使用code-server访问可以直接可视化编辑脚本及代码
  - 也可以使用vnc连接，但是本博主着实讨厌vnc，其一就是界面模糊，其二就是有时候会出现黑名单访问限制，遇到类似问题可以在评论区反馈。

- 更改`init.sh`脚本(非必要，但是好用)

  - `init.sh`脚本最后部分会将生成的证书copy到`example01`与`example02`中，而不会copy到`example03`中，而本次博客主要涉及`example03`，因此对`init.sh`脚本进行添加如下内容

  - ```bash
    fi
    if [ ! -d "${PWD}/app/example03_java/profiles/Org1/tls" ]; then 
        mkdir -p app/example03_java/profiles/Org1/tls
    fi
    if [ ! -d "${PWD}/app/example03_java/profiles/Org2/tls" ]; then 
        mkdir -p app/example03_java/profiles/Org2/tls
    fi
    
    cp ./organizations/peerOrganizations/org1.example.com/connection-org1.json app/example03_java/profiles/Org1/connection.json
    cp ./organizations/peerOrganizations/org2.example.com/connection-org2.json app/example03_java/profiles/Org2/connection.json
    cp ./organizations/peerOrganizations/org1.example.com/ca/ca.org1.example.com-cert.pem app/example03_java/profiles/Org1/tls/
    cp ./organizations/peerOrganizations/org2.example.com/ca/ca.org2.example.com-cert.pem app/example03_java/profiles/Org2/tls/
    ```

  - ![](http://fl.ljuuu.com/img/20210625000229.png)

  - 当然也可以不修改`init.sh`，这样每次`teardown`之后需要把别的项目中的`profile`证书文件拷贝到`example3`中即可。

## 2、chaincode部署及调用

- 一些小bug在之前的文章中也有讲过，可以去翻阅一些，这里就直接跳过了

  ```bash
  . ./teardown
  . ./init.sh
  . scripts/deploy_chaincode.sh java ${PWD}/chaincode/chaincode_example03/java mycc03_java
  ```

  ![](http://fl.ljuuu.com/img/20210625002836.png)

- 测试脚本

  - `. scripts/test_example03.sh mycc03_java`

  - ![](http://fl.ljuuu.com/img/20210625004530.png)

  - 同时调用一个方法两次，貌似排序结点只会保存一个结果。

    ![](http://fl.ljuuu.com/img/20210625004948.png)

## 3、SDK开发及隐私保护实例测试

- 将`example03_java`项目打包，注意：每次重新部署链码需要删除该文件夹下`wallet`文件夹

  ![](http://fl.ljuuu.com/img/20210625005725.png)

- 测试基本SDK

  ```bash
  java -classpath ./target/example03-1.0-SNAPSHOT-jar-with-dependencies.jar example03.EnrollAdmin
  java -classpath ./target/example03-1.0-SNAPSHOT-jar-with-dependencies.jar example03.RegisterUser
  java -classpath ./target/example03-1.0-SNAPSHOT-jar-with-dependencies.jar example03.InvokeQuery
  java -classpath ./target/example03-1.0-SNAPSHOT-jar-with-dependencies.jar example03.InvokeTransfer
  ```

  ![](http://fl.ljuuu.com/img/20210625010058.png)

- 隐私保护测试

  ```
  java -classpath ./target/example03-1.0-SNAPSHOT-jar-with-dependencies.jar example03.InvokeTopup
  java -classpath ./target/example03-1.0-SNAPSHOT-jar-with-dependencies.jar example03.InvokeQueryLocal
  java -classpath ./target/example03-1.0-SNAPSHOT-jar-with-dependencies.jar example03.InvokeQuery
  ```

  ![](http://fl.ljuuu.com/img/20210625012146.png)

  ![](http://fl.ljuuu.com/img/20210625012252.png)

