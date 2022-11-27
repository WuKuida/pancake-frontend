## 功能
添加新的流动性的时候：上面代币是X，下面的代币是Y，最后的公式是XY^0.75=K
Swap功能
1. 只有代币拥有者（项目方）和白名单有定价权，即第一次添加流动性只由代币拥有者和白名单添加，第一次添加流动性之后解除限制所有人能添加流动性。
2. 白名单可后台添加
3. Swap内部手续费 0.3%
4. 内部手续费分配0.05%回购原代币  0.05%+0.05%激励基金钱包  0.05%奖金库钱包 0.1%lp
5. 可升级，可更新迭代，上述分配数据在升级的过程中官方可调整
6. 有farm 
total 0.30%
回购钱包
0x0c7C1ddbaBA4143893E5a68912C9Ef6220ADEe8b 0.05%
激励钱包
0x90239826Bff178315CcA518B4FB1b4051cee2557 0.10%
奖金库钱包
0xE8eaD4E5a638882c1402aC616D53c05d38c4029c 0.05%
lp 0.10%

## 目录
contract：合约
addWhite：自动添加创建者到白名单中后台服务
.env.production：生产环境配置
src/state/mint/hooks.ts: 监听流动性相关的数据变化，并进行响应
src/state/mint/reducer.ts: 流动性相关的数据
src/state/mint/actions.ts: 定义了action的结构
src/views/AddLiquidity/ChoosePair.tsx: 选择pair对时的界面
src/views/AddLiquidity/index.tsx: 添加流动性时的界面
packages/swap-sdk/src/constants.ts: 配置的常量，例如合约地址这些都在这里配置
packages/swap-sdk/src/entities/pair.ts： pair类，获取pair的代币余额，以及指数
packages/swap-sdk/src/abis.IPancakePair.json： pair的abi

全局搜索exp diff，查找不同公式下需要修改的地方

### My_PRODUCT ADDRESS
- WBNB:            0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c
- PancakeFactory:  0xAfA51C871708Dd68029af5D07b889e9570A3DAC7
- INIT_CODE_HASH:  0x736c63951561f63b4a38f38b9bd33c4bcd695af12505b0e64b10364ea0fced24
- PancakeRouter:   0x6754705120f53682D7159439DF01fA8af1b326c7

### ORIGIN_PRODUCT ADDRESS
- WBNB:            0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c
- PancakeFactory:  0xcA143Ce32Fe78f1f7019d7d551a6402fC5350c73
- INIT_CODE_HASH:  0x00fb7f630766e6a796048ea87d01acd3068e8ff67d078148a3fa3f4a84f69bd5
- PancakeRouter:   0x10ed43c718714eb63d5aa57b78b54704e256024e

### MyTEST
- WBNB:            0xae13d989daC2f0dEbFf460aC112a837C89BAa7cd
- PancakeFactory:  0xd736D7D33433Ba8CeeEDC2022b4e44F7c83c98F1
- INIT_CODE_HASH:  0x2d738a6e5d690a74839138255202b9a25a988507b4c784ab9ee7c27b95386423
- PancakeRouter:   0x915c78F88789F6F488815B1CB3EBA224BcD7F7b3

### ORIGIN_TEST
- WBNB:            0xae13d989daC2f0dEbFf460aC112a837C89BAa7cd
- PancakeFactory:  0x6725f303b657a9451d8ba641348b6761a6cc7a17
- INIT_CODE_HASH:  0xd0d4c4cd0848c93cb4fd1f498d7013ee6bfb25783ea21593d5834f5d250ece66
- PancakeRouter:   0xd99d1c33f9fc3444f8101754abc46c52416550d1

## Quick Start
1. 需要安装nodejs，yarn，以下安装方式作为参考意见
[nodejs](https://nodejs.org/en/download/)
```
wget https://nodejs.org/dist/v18.12.1/node-v18.12.1-linux-x64.tar.xz
tar -xvJf node-v18.12.1-linux-x64.tar.xz
mv node-v18.12.1-linux-x64 /opt/node
cat >>~/.bashrc
export NODE_HOME=/opt/node
export PATH=$NODE_HOME/bin:$PATH
source ~/.bashrc
npm install --global yarn

git clone项目并且切换到对应分支
git checkout -b exp origin/exp
git checkout -b exp0.5 origin/exp0.5


```sh
yarn
```

start the development server
```sh
yarn dev
```

build with production mode
```sh
yarn build

# start the application after build
yarn start
```

## 部署流程
1. 部署myFactoryExponent.sol，编译器版本0.5.16
- 修改PancakeFactory内构造函数里面的exponentB，如果公式是XY^0.75=K, exponentB = 75;
- 如果是XY^0.5=K, exponentB = 50;
- 如果部署的时候提示代码过长，回到remix编译界面，选择高级设置，勾选Enable optimization
2. 获取INIT_PAIR_HASH_HASH
3. 将INIT_PAIR_HASH_HASH设置到router的PancakeLibrary-》pairFor中
4. 部署router，编译器版本0.6.6
5. 调用setRouterAddress，将router的地址设置到factory上
6. 调用setWETH将WBNB的地址设置到factory上
7. 进入到项目根目录，全局搜索Mytest下的WBNB，PancakeFactory，INIT_CODE_HASH，PancakeRouter地址替换成新的地址
- 如果是TEST环境，WBNB不用改，除非是eth
- 如果是线上环境，搜索 *PRODUCT下的地址，并全部替换成新的地址
8. 修改添加白名单中的账号
- 修改addWhite.py中的setter_address和setter_private_key，将其改成工厂合约的owner的地址和密钥
- 修改addWhite.py的node_url，改成自己的节点，当前节点后续会实效
9. 部署添加白名单后台服务，记得打开8868端口，支持http/https的请求，并记录当前ip地址
```
pip install tornado
nohup python3 addWhite.py >/dev/null 2>&1 &
```
13. 在src/views/AddLiquidity/index.tsx内搜索8868，将前面的ip地址，改称添加白名单后台服务所部署的ip地址

测试：
1. 创建一个新的ERC20 token 0x5cD149a8e33B31BFf20Bd7D49a8159D42E6BD419 0xF44e2FdBA41f86c2Ff2f0A447130D8c89697c2b8
2. approve router 使用新的token 100000000000000
3. addLiquidityETH 记得设置Value 
4. 确认添加不了，之后添加百名单，再次添加成功
5. SWAP刚刚添加的流动性 ["0xae13d989daC2f0dEbFf460aC112a837C89BAa7cd","0x5cD149a8e33B31BFf20Bd7D49a8159D42E6BD419"]
6. 添加结束了之后，使用其他账号添加流动性, 验证是否能够添加成功，且流动性费用是否安比例分给各个账号
7. 部署到以太网的时候，需要注意WETH


## 测试账号
地址1： 0x994D95Ea4C37C4b586Fa9668211Daa4Aa03be060
密钥1： 0x2a5f70d22e1ee3c94e8bdc4846c41d7f92b588cc1bafa8f96d68d0a3533d926c

地址2： 0x00F41A509458f85b9a272e0A8E81380a1Fc55334
密钥2： 0xbd513d90e1e2298be6b40f83081c7162d34ed641d3175e61633104e6cc15bc02

http://141.95.97.23:3000/ 验收环境