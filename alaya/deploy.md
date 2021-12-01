# Alaya部署

## 安装

### 系统要求

- Windows10
- Ubuntu 18.04.01及以上
- [Golang](https://golang.org/doc/install) :version 1.14+
- [cmake](https://cmake.org/) :version 3.0+
- [g++&gcc](http://gcc.gnu.org/) :version 7.4.0+

### go安装

~~~bash
# 下载 Go 压缩包
wget -c https://dl.google.com/go/go1.16.9.linux-amd64.tar.gz -O - | sudo tar -xz -C /usr/local && rm -rf go1.16.9.linux-amd64.tar.gz

# .bashrc 文件中(全局设置)
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
#
source .bashrc
~~~

### cmake安装

~~~bash
# 查看cmake版本
snap info cmake
# 安装指定版本
sudo snap install cmake --classic --channel=3.18/stable
# make安装
sudo apt install make 
~~~

### gcc安装

~~~bash
# ubuntu 18 需要先运行以下命令
sudo apt install software-properties-common
sudo add-apt-repository ppa:ubuntu-toolchain-r/test

# gcc 安装命令
sudo apt-get install gcc-8
sudo apt-get install g++-8

# 检查是否安装成功
gcc -v
g++ -v
~~~

配置

~~~bash
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-8 300
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-8 300
~~~

恢复默认版本

~~~bash
sudo update-alternatives --config gcc
sudo update-alternatives --config g++
~~~

### node安装

~~~bash
# 使用nvm管理
$ wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
$ nvm install 12.18.1
$ nvm alias default 12.18.1 

# 直接安装
$ wget https://nodejs.org/download/release/v12.18.1/node-v12.18.1-linux-x64.tar.gz
$ sudo tar -zxvf node-v12.18.1-linux-x64.tar.gz -C /usr/local
$ sudo ln -s /usr/local/node-v12.18.1-linux-x64/bin/* /usr/bin/ll
~~~

### 源码地址

**Alaya-Go**  的 **feature-confidential-token** 分支

[https://github.com/AlayaNetwork/Alaya-Go/tree/feature-confidential-token](https://github.com/AlayaNetwork/Alaya-Go/tree/feature-confidential-token)

### 编译

~~~bash
# 编译
cd Alaya-Go
go mod tidy
go mod download
make all
# 拷贝二进制 
sudo cp -f ./build/bin/platon /usr/bin/alaya
sudo cp -f ./build/bin/alayakey /usr/bin/
~~~

### 安装步骤参考

[https://alaya.network/alaya-devdocs/zh-CN/Install_Alaya/](https://alaya.network/alaya-devdocs/zh-CN/Install_Alaya/)



## 直接下载二进制文件

~~~bash
mkdir test && cd test && sudo wget https://raw.githubusercontent.com/fujianlian/Alaya-Go/feature-confidential-token/linux/alaya && sudo wget https://raw.githubusercontent.com/fujianlian/Alaya-Go/feature-confidential-token/linux/alayakey && sudo mv alaya /usr/bin && sudo mv alayakey /usr/bin && sudo chmod +x /usr/bin/alaya  /usr/bin/alayakey && cd .. && sudo rm -rf test && alaya version
~~~



## 私有服务单节点部署

[https://alaya.network/alaya-devdocs/zh-CN/Private_network](https://alaya.network/alaya-devdocs/zh-CN/Private_network)

~~~bash
mkdir alaya-node
cd alaya-node
~~~

### 生成nodekey和blskey等相关文件

~~~bash
mkdir -p ./alaya-node/data && alayakey genkeypair | tee >(grep "PrivateKey" | awk '{print $2}' > ./alaya-node/data/nodekey) >(grep "PublicKey" | awk '{print $3}' > ./alaya-node/data/nodeid) && alayakey genblskeypair | tee >(grep "PrivateKey" | awk '{print $2}' > ./alaya-node/data/blskey) >(grep "PublicKey" | awk '{print $3}' > ./alaya-node/data/blspub)
~~~

### 创建创世钱包

~~~bash
alaya --datadir ./data account new {wallet_name}
~~~

### 创世区块

在alaya-node目录下创建创世区块配置文件`alaya.json`

~~~json
{
    "config": {
        "chainId": 201030,
        "eip155Block": 3,
        "cbft": {
            "initialNodes": [{
                "node":"enode://your-node-id:16789",
                "blsPubKey":"your-node-blspubkey"
            }],
            "amount": 10,
            "period": 10000,
            "validatorMode": "ppos"
        },
        "genesisVersion": 3584
    },
    "economicModel": {
        "common": {
            "maxEpochMinutes": 3,
            "maxConsensusVals": 4,
            "additionalCycleTime": 28
        },
        "staking": {
            "stakeThreshold": 10000000000000000000000,
            "operatingThreshold": 1000000000000000000,
            "maxValidators": 5,
            "unStakeFreezeDuration": 2,
            "rewardPerMaxChangeRange": 1000,
            "rewardPerChangeInterval": 2
        },
        "slashing": {
            "slashFractionDuplicateSign": 100,
            "duplicateSignReportReward": 50,
            "slashBlocksReward": 5,
            "maxEvidenceAge": 1,
            "zeroProduceCumulativeTime": 1,
            "zeroProduceNumberThreshold": 1,
            "zeroProduceFreezeDuration":1
        },
        "gov": {
            "versionProposalVoteDurationSeconds": 1600,
            "versionProposalSupportRate": 6670,
            "textProposalVoteDurationSeconds": 160,
            "textProposalVoteRate": 5000,
            "textProposalSupportRate": 6670,
            "cancelProposalVoteRate": 5000,
            "cancelProposalSupportRate": 6670,
            "paramProposalVoteDurationSeconds": 160,
            "paramProposalVoteRate": 5000,
            "paramProposalSupportRate": 6670
        },
        "reward": {
            "newBlockRate": 50,
            "platONFoundationYear": 2,
            "increaseIssuanceRatio": 500
        },
        "restricting": {
            "minimum_release": 80000000000000000000
        },
        "innerAcc": {
            "platonFundAccount": "atx1m6n2wj6cmnnqnmhhaytyf6x75607t7rhszeuhk",
            "platonFundBalance": 2500000000000000000000000,
            "cdfAccount": "atx1ur2hg0u9wt5qenmkcxlp7ysvaw6yupt4xerq62",
            "cdfBalance": 500000000000000000000000
        }
    },
    "nonce": "0x0376e56dffd12ab53bb149bda4e0cbce2b6aabe4cccc0df0b5a39e12977a2fcd23",
    "timestamp": "0x175301efa90",
    "extraData": "0xd782070186706c61746f6e86676f312e3131856c696e757800000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
    "gasLimit": "0x800000000",  // 依据实际情况设置大小
    "alloc": {
        "your-account-address": {
            "balance": "9999999000000000000000000"
        }
    },
    "number": "0x0",
    "gasUsed": "0x0",
    "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}
~~~

修改 `your-node-pubkey` 为之前生成的***节点公钥(nodeid)\***，`your-node-blspubkey` 为 ***节点bls公钥(blspub)\***，`your-account-address`为创建钱包的地址（可以配置多个初始账户）

~~~bash
# 初始化创世区块
alaya --datadir ./data init alaya.json
~~~

出现`Successfully wrote genesis state`相关提示说明初始化创世信息完成。

### 启动节点

~~~bash
cd ~/alaya-node && nohup alaya --identity "alaya1" --datadir ./data --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,platon,net,web3,admin,personal" --rpc --nodiscover --nodekey ./data/nodekey --cbft.blskey ./data/blskey > ./data/alaya.log 2>&1 &
~~~

### 检查节点运行状态

```shell
alaya attach http://localhost:6789 --exec platon.blockNumber
```

多执行几次上面的命令，如果块高一直在增长，表示单节点私链部署成功。



## 私有服务集群网络部署

~~~bash
mkdir -p ~/alaya/alaya-node/data0 ~/alaya/alaya-node/data1 ~/alaya/alaya-node/data2
~~~

### 生成nodekey和blskey等相关文件

~~~bash
cd ~/alaya/alaya-node && export d=data0 && mkdir -p $d && alayakey genkeypair | tee >(grep "PrivateKey" | awk '{print $2}' > $d/nodekey) >(grep "PublicKey" | awk '{print $3}' > $d/nodeid) && alayakey genblskeypair | tee >(grep "PrivateKey" | awk '{print $2}' > $d/blskey) >(grep "PublicKey" | awk '{print $3}' > $d/blspub)
~~~

**将 ./data0 修改为 ./data1和./data2 再次运行**，有几个就执行几次

### 创建创世钱包

~~~bash
export d=data2 && alaya --datadir ~/alaya/alaya-node/$d account new 
~~~

### 创世区块

与单节点部署一致，主要变动如下

~~~json
# 此处为片段
...
  "cbft": {
  "initialNodes": [{
        "node": "enode://node0-pubkey@0.0.0.0:16787",
        "blsPubKey": "node0-blspubkey"
    },{
        "node": "enode://node1-pubkey@0.0.0.0:16788",
        "blsPubKey": "node1-blspubkey"
    },{
        "node": "enode://node1-pubkey@0.0.0.0:16789",
        "blsPubKey": "node2-blspubkey"
    }],
...
  "alloc": {
    // 可以配置多个初始账户
    "atx12vt5sjef39u5wd4uf4hkj8fmayle55qam8fexs": {
        "balance": "99999999000000000000000000"
    }
  },
...
~~~

### 初始化和启动

分别为节点0、节点1和节点2初始化创世块信息：

```bash
cd ~/alaya/alaya-node && alaya --datadir ./data0 init alaya.json && alaya --datadir ./data1 init alaya.json && alaya --datadir ./data2 init alaya.json
```

初始化成功后，分别用nohup方式启动节点0、节点1和节点2：

```bash
cd ~/alaya/alaya-node && 
nohup alaya --identity "alaya0" --datadir ./data0 --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,platon,net,web3,admin,personal" --rpc --nodiscover --nodekey ./data0/nodekey --cbft.blskey ./data0/blskey > ./data0/alaya.log 2>&1 &
&&
nohup alaya --identity "alaya1" --datadir ./data1 --port 16790 --rpcaddr 0.0.0.0 --rpcport 6790 --rpcapi "db,platon,net,web3,admin,personal" --rpc --nodiscover --nodekey ./data1/nodekey --cbft.blskey ./data1/blskey  > ./data1/alaya.log 2>&1 &
&&
nohup alaya --identity "alaya2" --datadir ./data2 --port 16791 --rpcaddr 0.0.0.0 --rpcport 6791 --rpcapi "db,platon,net,web3,admin,personal" --rpc --nodiscover --nodekey ./data2/nodekey --cbft.blskey ./data2/blskey  > ./data2/alaya.log 2>&1 &
&& 
```

检查节点运行状态，参照单节点部署流程

