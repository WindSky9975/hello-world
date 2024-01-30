 之前github项目拉取超时，最后我搞了好久才成功，希望能够帮助大家。 

注：所有命令均在gitbash中输入

 ![img](https://img-blog.csdnimg.cn/direct/94d8d3fe8e7b437dbc4aa38aa4390e80.png)

# 一、 步骤一

有时，防火墙完全拒绝允许SSH 连接。如果无法选择使用带有凭据缓存的HTTPS克隆，您可以尝试使用通过HTTPS端口建立的SSH连接进行克隆。大多数防火墙规则应该允许这样做，但代理服务器可能会干扰。

要测试是否可以通过 HTTPS 端口进行 SSH，请运行以下 SSH 命令：

```bash
ssh -T -p 443 git@ssh.github.com
```

我遇见的情况是这样的：

```bash
The authenticity of host '[ssh.github.com]:443 ([20.205.243.160]:443)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:4: github.com
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```



这个提示是在SSH连接时的服务器身份验证过程中出现的。它告诉你，目前还没有保存该服务器的公钥，因此无法确认连接的主机是否真实可信。你需要进行确认以确保你连接到的是正确的 GitHub 服务器。

接下来，你可以选择是否接受该主机的公钥。如果你信任该主机，可以输入 `yes` 并按回车，以保存该主机的公钥到你的本地 SSH 已知主机列表中。然后，下次连接时就不会再提示这个信息，除非服务器的密钥发生变化。

在选择yes后会显示：

```bash
Warning: Permanently added '[ssh.github.com]:443' (ED25519) to the list of known hosts.
Connection closed by 20.205.243.160 port 443
```



这表示 SSH 客户端已经将 GitHub 的 SSH 服务器的公钥添加到你的本地 "known_hosts" 文件，以后连接时将不再显示类似的警告，除非服务器的密钥发生变化。

然后再输入之前的命令：

```bash
ssh -T -p 443 git@ssh.github.com
```



这时会弹出：

```bash
> Hi USERNAME! You've successfully authenticated, but GitHub does not provide shell access.
```



这时候如果发现还是无法拉取，就进行下一步 

# 二、步骤二

这时候我们还是发现无法拉取，检查一下你是否有具体的配置文件`config，config` 文件一般在 `~/.ssh/` 或者 `C://Users//<username>//.ssh` 目录下面，没有后缀名。如果在上述目录中找不到 `config` 文件，那么可以新建一个。

要查看或编辑 SSH 配置文件，你可以执行以下步骤：

## 1.打开 SSH 配置文件

```bash
nano ~/.ssh/config
```



## 2.如果文件不存在，你可以创建一个

## 3.进入用户主目录

```bash
cd ~
```



## 4.检查是否有 `.ssh` 文件夹。如果没有，你可以创建一个

```bash
mkdir ~/.ssh
```



## 5. 进入 `.ssh` 文件夹

```bash
cd ~/.ssh
```



## 6.创建 `config` 文件。你可以使用文本编辑器，如 `nano` 或 `vi`

```bash
nano config
```



或者使用其他编辑器，例如：

```bash
vi config
```



然后再将这些添加上去：

```bash
Host github.com
    Hostname ssh.github.com
    Port 443
    User git
```



我这里是使用nano来编辑的，如果 `config` 文件已经存在，编辑它；否则，创建新文件并开始编辑。![img](https://img-blog.csdnimg.cn/direct/94d8d3fe8e7b437dbc4aa38aa4390e80.png)

## 7.保存文件并关闭文本编辑器

使用 `nano` 编辑器时，你可以按 `Ctrl + X` 来退出，然后按 `Y` 来确认保存，最后按 `Enter` 完成。使用 `vi` 编辑器时，你可以按 `Esc` 键，然后输入 `:wq` 来保存并退出。

## 8.你可以通过再次连接到 GitHub.com 来测试这是否有效

```bash
ssh -T git@github.com
```



如果成功，则会显示以下信息：

```bash
Hi USERNAME! You've successfully authenticated, but GitHub does not provide shell access.
```



## 9.现在可以试一试拉取代码啦

```bash
git clone git@github.com:WindSky9975/hello-world.git
```



## 

信息来源：[github文档ssh故障排除](https://docs.github.com/en/authentication/troubleshooting-ssh/using-ssh-over-the-https-port)