---
timezone: UTC+8
---

# zhuoyu18

**GitHub ID:** zhuoyu18

**Telegram:**

## Self-introduction

Let’s vibe Reactive dApp

## Notes

<!-- Content_START -->
# 2026-03-09
<!-- DAILY_CHECKIN_2026-03-09_START -->
# **配置环境**

一、创建.env格式的文件，然后按照名字=值的格式输入。

（ORIGIN\_RPC="你的起源链RPC地址" ORIGIN\_CHAIN\_ID="你的起源链ID" ORIGIN\_PRIVATE\_KEY="你在起源链上用于签名交易的私钥" DESTINATION\_RPC="你的目标链RPC地址" DESTINATION\_CHAIN\_ID="你的目标链ID" DESTINATION\_PRIVATE\_KEY="你在目标链上用于签名交易的私钥" REACTIVE\_RPC="Reactive Network的RPC地址" REACTIVE\_PRIVATE\_KEY="你在Reactive Network上用于签名交易的私钥" SYSTEM\_CONTRACT\_ADDR="Reactive Network上的服务地址" DESTINATION\_CALLBACK\_PROXY\_ADDR="目标链上的回调代理服务地址" # 以下两个变量是你部署完合约后需要填写的，可以先留空或者写上占位符 # ORIGIN\_ADDR="你的BasicDemoL1Contract部署后的地址" # CALLBACK\_ADDR="你的BasicDemoL1Callback部署后的地址"）

`ORIGIN_RPC` / `DESTINATION_RPC 和 ORIGIN_CHAIN_ID` / `DESTINATION_CHAIN_ID 在 Chainlist` 网站上搜索出测试网站，然后选择两个不同的ORIGIN和DESTINATION的值。

**key**：1在metamask插件创建新钱包后会生成助记词，要妥善保管.2在 MetaMask 的网络选择器中，选择你将要用于测试的特定测试网，例如 `Sepolia test network` 或 `Base Sepolia Testnet`。3点击你新创建的测试账户，然后点击账户名称旁边的复制图标，复制新测试钱包地址。

1.  访问水龙头 (Faucet) 网站： 不同的测试网有不同的水龙头。
    
    -   Sepolia ETH 水龙头： 搜索 “Sepolia Faucet” (例如 `sepoliafaucet.com` 或 `faucetlink.to/sepolia`)。你可能需要登录 GitHub 或提供一些证明（比如在主网上有一些 ETH）才能领取。
        
    -   Base Sepolia ETH 水龙头： 搜索 “Base Sepolia Faucet” (例如 `faucet.base.org`)。
        
2.  粘贴地址并领取： 将你的测试钱包地址粘贴到水龙头网站的输入框中，并按照指示领取测试代币。
    
3.  在 MetaMask 中确认： 领取成功后，你的 MetaMask 钱包中会显示相应测试网络的测试代币余额。
    

导出测试钱包的私钥，能将私钥填入 `.env` 文件。

1.  在 MetaMask 中选择你的测试账户： 确保当前选中的是你在步骤一中创建的“Test Wallet”。
    
2.  点击账户旁边的三个点 `...` 菜单。
    
3.  选择 “账户详情” (Account details)。
    
4.  点击 “导出私钥” (Export Private Key)。
    
5.  输入你的 MetaMask 密码。
    
6.  复制显示的私钥。 它通常是一个 `0x` 开头的长十六进制字符串。
    

将私钥填入 `.env` 文件

将你从起源链测试钱包导出的私钥赋值给 `ORIGIN_PRIVATE_KEY`，将你从目标链测试钱包导出的私钥赋值给 `DESTINATION_PRIVATE_KEY`。

\# ... 其他变量 ... ORIGIN\_PRIVATE\_KEY="你从Sepolia测试钱包导出的私钥" DESTINATION\_PRIVATE\_KEY="你从Base Sepolia测试钱包导出的私钥" # ...

### **如何把** `.env` **文件添加到** `.gitignore` **中？**
<!-- DAILY_CHECKIN_2026-03-09_END -->
<!-- Content_END -->
