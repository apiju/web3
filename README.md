# 产品需求
内部版本没有kyc，注册流程
C2N launchpad是一个区块链上的一个去中心化发行平台，专注于启动和支持新项目。它提供了一个平台，允许新的和现有的项目通过代币销售为自己筹集资金，同时也为投资者提供了一个参与初期项目投资的机会。下面是C2N launchpad产品流程的大致分析：
1. 项目申请和审核
- 申请：项目方需要在C2N launchpad上提交自己项目的详细信息，包括项目介绍、团队背景、项目目标、路线图、以及如何使用筹集的资金等。
- 审核：C2N launchpad团队会对提交的项目进行审核，评估项目的可行性、团队背景、项目的创新性、以及社区的兴趣等。这一过程可能还包括与项目方的面对面或虚拟会议。
2. 准备代币销售
- 设置条款：一旦项目被接受，C2N launchpad和项目方将协商代币销售的具体条款，包括销售类型（如公开销售或种子轮）、价格、总供应量、销售时间等。
- 准备市场：同时，项目方需要准备营销活动来吸引潜在的投资者。C2N launchpad也可能通过其平台和社区渠道为项目提供曝光。
3. KYC和白名单
- KYC验证：为了符合监管要求，参与代币销售的投资者需要完成Know Your Customer（KYC）验证过程。
- 白名单：完成KYC的投资者可能需要被添加到白名单中，才能在代币销售中购买代币。
4. 代币销售
- 销售开启：在预定时间，代币销售开始。根据销售条款，投资者可以购买项目方的代币。
- 销售结束：销售在达到硬顶或销售时间结束时关闭。
5. 代币分发
- 代币分发：销售结束后，购买的代币将根据约定的条款分发给投资者的钱包。

用户质押平台币，获得参与项目IDO的购买权重，后端配置项目信息并操作智能合约生成新的sale，用户在sale开始之后进行购买，项目结束后，用户进行claim


# 项目说明
该代码仓库共分为两个项目，分别为Farm（质押挖矿,流动性挖矿）与 Sale（项目IDO)

质押挖矿（Farming）的概念

质押挖矿是指用户将流动性提供（LP）代币存入一个智能合约（称为农场，Farm）中，来获取特定的ERC20代币奖励的过程。该过程通常包括以下几个主要步骤：

    存入LP代币：
    用户将流动性提供（LP）代币存入农场合约中。存入的LP代币代表用户在去中心化交易所（如Uniswap或SushiSwap）中提供的流动性。
    
    获取奖励：
    用户根据其质押的LP代币数量和时间，按比例获得ERC20代币奖励。奖励是根据区块时间或秒计算的。
    
    提取LP代币和奖励：
    用户可以随时提取其质押的LP代币，并获取其累积的ERC20代币奖励。

合约中的具体运作机制

合约FarmingC2N具体实现了上述过程，以下是该合约的一些关键点：

    结构体：
        UserInfo：存储每个用户的质押数量和奖励债务。
        PoolInfo：存储每个流动性池的信息，包括LP代币合约地址、分配点数、最后奖励计算时间、每股累积的ERC20奖励和总质押数量。
    
    核心变量：
        erc20：奖励代币的合约地址。
        rewardPerSecond：每秒奖励的ERC20代币数量。
        startTimestamp和endTimestamp：质押挖矿的开始和结束时间。
        totalRewards：农场总奖励的ERC20代币数量。
        totalAllocPoint：所有流动性池的总分配点数。
    
    主要函数：
        fund：向农场添加奖励代币，并延长质押挖矿的结束时间。
        add：添加新的流动性池。
        set：更新流动性池的分配点数。
        deposit：用户存入LP代币并更新其奖励。
        withdraw：用户提取LP代币并获取奖励。
        emergencyWithdraw：紧急情况下，用户可以提取其所有质押的LP代币，但不会获取奖励。
        pending：查看用户的待领取奖励。
        updatePool和massUpdatePools：更新流动性池的奖励变量。

操作流程示例

    管理员添加奖励代币：
    管理员调用fund函数向农场添加奖励代币，并设置结束时间。
    
    用户质押LP代币：
    用户调用deposit函数，将LP代币存入农场。
    
    用户获取奖励：
    用户调用withdraw函数，提取其质押的LP代币并获取相应的ERC20代币奖励。
    
    紧急提取：
    在紧急情况下，用户可以调用emergencyWithdraw函数，提取所有质押的LP代币，但不会获取任何奖励。

通过这个智能合约，质押挖矿提供了一种激励机制，使用户能够通过提供流动性来获得额外的代币奖励。


# Farm 部署流程

1. 复制.env.example 到.env,修改PRIVATE_KEY, 要求arbitrum sepolia上有测试eth

2. 部署c2n token
   `npx hardhat run scripts/deployment/deploy_c2n_token.js --network sepolia`

3. 部署airdrop合约
   `npx hardhat run scripts/deployment/deploy_airdrop_c2n.js --network sepolia`

4. 修改前端地址，运行前端测试airdrop功能


进入前端目录c2n-fe，安装依赖
`yarn`

修改token地址和airdrop合约地址为合约之前部署的两个地址，如下：

c2n-fe/src/config/index.js 中的
`AIRDROP_TOKEN_ADDRESS_MAP` 的 31337（本地链端口）地址修改为C2N-TOKEN的地址
`AIRDROP_CONTRACT` Airdrop-C2N的地址

运行项目
`yarn dev`

修改前端本地链地址
默认本地链rpc地址为：http://127.0.0.1:8545
链ID为31337
c2n-fe/src/util/chain_id.ts
c2n-fe/src/config/valid_chains.js
如有更换，在这两个文件中修改本地链ID和rpc地址

6. farm

修改c2n-contracts/scripts/deployment/deploy_farm.js
第7行startTS为3分钟之后（必须是当前时间之后，考虑上链网络延迟）

修改 c2n-fe/src/config/farms.js
depositTokenAddress和earnedTokenAddress为AIRDROP_TOKEN的地址
修改stakingAddress为部署的farm合约地址：地址在\c2n-contracts\scripts\deployment\deploy_farm.js\里面有日志，盯着点

部署完毕，可以使用账号体验farm功能

## 具体操作
1. Farm 流程需要用到我们的Erc20测试代币C2N, 可以在首页领取C2N(一个账户只能领取一次),并且添加到我们metamask，添加之后我们可以在metamask 看到我们领取的C2N 代币

2. 在我们farm界面，我们可以质押fc2n 代币获取c2n, (方便大家操作，我们的测试网fc2n，c2n 是在上一步中领取的同一代币)，在这里我们有三个操作，stake:质押，unstake(withdraw):撤回质押， 以及 claim:领取奖励;

点击stake 或者claim 进入对应的弹窗，切换tab可以进行对应的操作；
3. Stake ，输入要质押的FC2N代币数量，点击stake 会唤起钱包，在钱包中confirm，然后等待交易完成；

我们新增质押了1FC2N,交易完成之后我们会看到，My staked 从0.1 变成1.1;
Total staked 的更新是一个定时任务，我们需要等待一小段时间之后才能看到更新

3. Claim 领取质押奖励的C2N,点击claim 并且在钱包确认

交易完成后我们会看到Available的FC2N数量增加了96，钱包里面C2N的代币数量同样增加了96

4. Unstake(withdraw),输入需要撤回的FC2N 数量(小于已经质押的Balance)，点击withdraw，并且在钱包确认交易


# 任务重难点分析

在上述的智能合约代码中，奖励机制的核心功能围绕着分配ERC20代币给在不同流动性提供池（LP pools）中质押LP代币的用户。这个过程涉及多个关键步骤和计算，用以确保每个用户根据其质押的LP代币数量公平地获得ERC20代币奖励。下面将详细解释这个奖励机制的实现过程。
## 奖励计算原理
1. 用户信息（UserInfo）和池子信息（PoolInfo）：
  - UserInfo 结构存储了用户在特定池子中质押的LP代币数量（amount）和奖励债务（rewardDebt）。奖励债务表示在最后一次处理后，用户已经计算过但尚未领取的奖励数量。
  - PoolInfo 结构包含了该池子的信息，如LP代币地址、分配点（用于计算该池子在总奖励中的比例）、最后一次奖励时间戳、累计每股分配的ERC20代币数（accERC20PerShare）等。
2. 累计每股分配的ERC20代币（accERC20PerShare）的计算：
  - 当一个池子接收到新的存款、提款或奖励分配请求时，系统首先调用updatePool函数来更新该池子的奖励变量。
  - 计算从上一次奖励到现在的时间内，该池子应分配的ERC20代币总量。这个总量是基于时间差、池子的分配点和每秒产生的奖励量来计算的。
  - 将计算出的奖励按照池子中总LP代币数量平分，更新accERC20PerShare，确保每股的奖励反映了新加入的奖励。
3. 用户奖励的计算：
  - 当用户调用deposit或withdraw函数时，合约首先计算用户在这次操作前的待领取奖励。
  - 待领取奖励是通过将用户质押的LP代币数量乘以池子的accERC20PerShare，然后减去用户的rewardDebt来计算的。这样可以得到自上次用户更新以来所产生的新奖励。
  - 用户完成操作后，其amount（如果是存款则增加，如果是提款则减少）和rewardDebt都将更新。新的rewardDebt是用户更新后的LP代币数量乘以最新的accERC20PerShare。
## 奖励发放
- 在用户进行提款（withdraw）操作时，计算的待领取奖励会通过erc20Transfer函数直接发送到用户的地址。
- 这种奖励分配机制确保了用户每次质押状态变更时，都会根据其质押的时间和数量公平地获得相应的ERC20代币奖励。
通过这种设计，智能合约能够高效且公平地管理多个LP池子中的奖励分配，使得用户对质押LP代币和领取奖励的过程感到透明和公正。

## c2n-contacts Makefile命令解释
将脚本命令简化成了make直接可执行的命令，写到了makefile中

需要安装`make`编译工具，如果没有安装，直接执行makefile文件中的js命令也可以

命令列表：
farm流程涉及执行的命令
- farm
AllocationStaking & ido流程涉及执行的命令
- ido
- sales
- deposit
启动本地测试链
- node
运行单测
- runtest
升级合约（暂未使用）
- upgrades
