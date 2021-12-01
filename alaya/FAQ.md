# FAQ

###  创世配置文件从哪获取？

拷贝 Alaya-Go的feature-confidential-token分支下json文件，cases/deploy/template/genesis_template.json，补全initialNodes和alloc信息

### 部署后的节点只能本机访问，其他来源访问不到？

这是因为创世配置文件或者nohub启动节点了限制了IP地址为：127.0.0.1，导致只能本机访问，建议采用IP地址：0.0.0.0

可以通过在terminal中运行 netstat -anpt 来检查是否符合情况

~~~bash
ubuntu@test:~$ netstat -anpt
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
...
# 外部可访问
tcp6       0      0 :::6789                 :::*                    LISTEN      1181/alaya
# 外部不可访问
tcp6       0      0 127.0.0.1:6789          :::*                    LISTEN      1181/alaya
...
~~~

### swap-contracts合约部署问题

部署参考： [https://alaya.network/alaya-devdocs/zh-CN/DApp_migrate/](https://alaya.network/alaya-devdocs/zh-CN/DApp_migrate/)

**1、Returned error: authentication needed: password or unlock**

账号未解锁，可以参照以下方式解锁

第一种：出现true即解锁成功，

~~~bash
fujianlian@ubuntu:~$ alaya attach http://localhost:6789
Welcome to the PlatON JavaScript console!

instance: PlatONnetwork/alaya/v0.14.0-unstable-e12f9a6c/linux-amd64/go1.17.2
at block: 1210 (Sun, 23 Jun 53793 06:27:57 PDT)
 datadir: /home/fujianlian/alaya-node/data
 modules: admin:1.0 net:1.0 personal:1.0 platon:1.0 rpc:1.0 web3:1.0
# 账号
# 密码
# 解锁周期（解锁的账户会保存到内存中直到解锁周期超时。默认的解锁周期为300秒。如果将解锁周期设为0s，将永久解锁账户,直到节点被关闭）
> personal.unlockAccount('atx1537t2wf92alz25vum65krffqyqhf9uw4h44vzf','1234',999999)
true
~~~

第二种：参照  [以下文章](https://devdocs.alaya.network/alaya-devdocs/zh-CN/Solidity_Getting_started#%E9%83%A8%E7%BD%B2helloworld%E5%90%88%E7%BA%A6) 中的  **step3.解锁钱包账户**  步骤

**2、Returned error: insufficient funds for gas * price + value.**

手续费不足，账户余额不够

**3、"..." ran out of gas (using a value you set in your network config or deployment parameters.)**

gas被耗尽，可能是链gas_limit设置小，修改重新部署后即可；也可能是truffle-config.js中的gas设置小，修改gas值重新编译



web3.platon.personal.importRawKey("c58b07b0360681b0597ce1042a5d18ed9eaf94346a0ab82e55428ea868c5b1cc","12345678")

web3.platon.personal.unlockAccount('0xdd222bd6c34c5bfff6033fbf0c3ce68a2a469460','12345678',999999)







