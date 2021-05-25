## Git基本操作

### 配置信息设置

- 设置全局用户名

    ```bash
    $ git config --global user.name "userName"
    ```

- 设置全局邮箱

    ```bash
    $ git config --global user.email "userEmail"
    ```

- 查看设置好的用户名

    ```bash
    $ git config user.name
    ```

- 查看设置好的邮箱

    ```bash
    $ git config user.email
    ```

### 创建本地仓库

- 创建 一个空目录

    ```bash
    $ mkdir repositoryName
    ```

- 进入仓库

    ```bash
    $ cd repositoryName
    ```

- 使用 Git 管理仓库

    ```bash
    $ git init
    ```

- 添加文件到仓库

    ```bash
    $ git add fileName
    ```

- 将文件提交到仓库

    ```bash
    $ git commit -m "Message"
    ```

### 修改文件

- 查看当前仓库状态

    ```bash
    $ git status
    ```

- 查看文件修改状态

    ```bash
    $ git diff fileName
    ```

- 将已修改的文件添加到仓库

    ```bash
    $ git add fileName
    ```

- 将已修改的文件提交到仓库

    ```bash
    $ git commit -m "Message"
    ```

### 版本回退

- 展示版本

    ```bash
    $ git log
    ```

- 只显示提交 ID

    ```bash
    $ git log --pretty=oneline
    ```

- 

