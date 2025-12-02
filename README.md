# GPTScan——《区块链金融安全》期末大作业代码复现

# 原readme.md
## 项目简介

使用 ChatGPT 检测智能合约中的逻辑漏洞。

## 使用方法

1. 安装依赖项：

- 需要 Python 3.10 或更高版本
- 安装 Python 依赖：`pip install -r requirements.txt`
- 需要 Java 17 或更高版本

2. 运行 GPTScan

在开始之前，请先选择正确的 Solidity 编译器（SOLC）版本，运行以下命令：

```shell
solc-select install 0.8.19 # 以 0.8.19 版本为例
solc-select use 0.8.19
```

例如，如果源代码存放在 `/source` 目录中，运行以下命令：

```shell
python3.10 main.py -s /sourcecode -o /sourcecode/output.json -k OPEN_AI_API_KEY_xxxxxxxxxxxxx
```

3. 查看输出结果

输出结果将保存在 `-o` 参数指定的路径下。以上述命令为例，结果将保存在 `/sourcecode/output.json` 文件中。

## 支持的项目类型

当前支持的项目类型包括：

- **单文件项目**：即一个文件夹（如 `contract`）中仅包含一个 `.sol` 文件（如 `example.sol`）。请传入**文件夹路径**作为源路径（**不要直接传入文件路径，否则可能导致错误**）。
- ~~多文件项目（暂不支持）~~：即一个目录中包含多个 `.sol` 文件，且无外部依赖。
- **主流开发框架项目**：如 Truffle、Hardhat、Brownie 等。

已测试兼容的框架包括：
- Hardhat
- Truffle
- Brownie

注意：本项目**不包含编译环境**（如 Node.js），需用户自行安装。

**重要提示**：请确保你的路径中**不要包含**如下关键词：`external`、`openzeppelin`、`uniswap`、`pancakeswap`、`legacy`。因为我们在路径匹配时采用了较为简单的方式（详见 `src/antlr4helper/callgraph.py:__parse_all_files`）。虽然不会报错，但会导致输出为空。

## 数据集

论文中用于评估 GPTScan 的数据集如下：

1. Web3Bugs: [https://github.com/MetaTrustLabs/GPTScan-Web3Bugs](https://github.com/MetaTrustLabs/GPTScan-Web3Bugs)  
2. DefiHacks: [https://github.com/MetaTrustLabs/GPTScan-DefiHacks](https://github.com/MetaTrustLabs/GPTScan-DefiHacks)  
3. Top200: [https://github.com/MetaTrustLabs/GPTScan-Top200](https://github.com/MetaTrustLabs/GPTScan-Top200)

# 实际测试流程
GPTScan 是一个用 AI（GPT）结合静态分析来检测智能合约逻辑漏洞的工具，它特别适合用 Web3Bugs 数据集测试，因为数据集里有真实的项目和已知漏洞标签。
### 第一步：准备电脑环境（10-20 分钟）

1. **安装 Python 3.10 或更高版本**：

   - 如果你是 Windows/Mac/Linux，去官网 https://www.python.org/downloads/ 下载安装。
   - 安装后，在命令行（终端）输入 `python --version` 检查，确保是 3.10+。

2. **安装 Java 17 或更高版本**（GPTScan 需要它来解析代码）：

   - 去 https://www.oracle.com/java/technologies/downloads/ 下载 JDK 17。
   - 安装后，输入 `java --version` 检查。

3. **安装 Solidity 编译器版本管理器**（用于编译合约）：

   - 在命令行运行：

     ```
     pip install solc-select
     solc-select install 0.8.19
     solc-select use 0.8.19
     ```

   - 这设置了 Solidity 0.8.19 版本（常见版本）。

### 第二步：下载和安装 GPTScan（5 分钟）

1. 打开命令行，运行：

   ```
   git clone https://github.com/GPTScan/GPTScan
   cd GPTScan
   pip install -r requirements.txt
   ```

   - `git clone` 下载代码（如果没装 Git，去 https://git-scm.com/ 安装）。
   - `pip install` 安装依赖包（需要联网）。

2. **获取 OpenAI API 密钥**（免费试用，但有费用）：

   - 去 https://platform.openai.com/ 注册账户（用邮箱）。
   - 在左侧菜单点 “API keys”，创建新密钥（sk- 开头）。
   - **重要**：保存好密钥，别分享！测试时会用一点点钱（每千行代码约 0.01 美元）。

### 第三步：下载 Web3Bugs 数据集（2 分钟）

1. 在命令行运行：

   ```
   git clone https://github.com/ZhangZhuoSJTU/Web3Bugs
   ```

   - 数据集下载到 `Web3Bugs` 文件夹。
   - 里面有 `contracts/` 文件夹，存着 72 个有漏洞的项目（每个项目是一个子文件夹，比如 `contest1/project1`）。
   - 还有 `results/bugs.csv`，这是漏洞标签文件，用来对比结果。

### 第四步：用 GPTScan 测试数据集（核心部分，第一次试 10-30 分钟）

1. **从小项目开始测试**（别一下测全部，72 个项目太多了，先挑一个）：

   - 进 Web3Bugs 文件夹：`cd Web3Bugs/contracts`（看里面有哪些子文件夹）。
   - 挑一个简单项目，比如第一个子文件夹（假设叫 `project1`）。

2. **运行 GPTScan**：

   - 在 GPTScan 文件夹下（别换目录），运行这个命令（替换路径和你的 API 密钥）：

     ```
     python3.10 main.py -s /path/to/Web3Bugs/contracts/project1 -o /path/to/output_project1.json -k sk-your-openai-api-key-here
     ```

     - `-s`：输入项目路径（合约代码文件夹）。
     - `-o`：输出结果文件（JSON 格式，里面有检测到的漏洞）。
     - `-k`：你的 OpenAI 密钥。

   - 示例（假设路径是本机）：

     ```
     python3.10 main.py -s ../Web3Bugs/contracts/contest1/project1 -o results/output1.json -k sk-abc123...
     ```

   - 运行时间：一个项目几分钟到十几分钟，取决于大小。

3. **查看结果**：

   - 打开 `output.json`（用记事本或 VS Code）。
   - 里面会列出检测到的逻辑漏洞，比如 “Risky First Deposit” 或 “Price Manipulation”，包括函数名、为什么有漏洞。
   - 去 `bugs.csv` 查这个项目，看标签（O/L/S 类别），对比 GPTScan 是不是找对了（比如 S 类是高级漏洞，GPTScan 擅长）。

4. **批量测试多个项目**（如果你想测更多）：

   - 可以写个简单脚本循环运行（Python 新手也行）：

     ```
     # 在 GPTScan 文件夹新建 test_all.py
     import os
     projects = ['project1', 'project2']  # 替换成你的项目名列表
     for proj in projects:
         os.system(f'python3.10 main.py -s ../Web3Bugs/contracts/{proj} -o results/{proj}_output.json -k sk-your-key')
     ```

     - 运行 `python test_all.py`。

   - 注意：项目不能有外部依赖（如 OpenZeppelin），如果有，GPTScan 可能会跳过。

### 第五步：分析和学习

- **对比准确率**：GPTScan 在 Web3Bugs 上召回率 83%（找出了大部分真漏洞），精确率 57%（有些假阳性，但静态分析帮过滤了）。
- 如果结果不对，检查日志（命令行输出）或试不同 Solidity 版本。
- 读读论文（https://arxiv.org/pdf/2308.03314）了解漏洞类型，比如 “Risky First Deposit” 是啥。
- 遇到错误？常见是路径错或 API 密钥无效——复制命令时检查空格。


## 引用

```bibtex
@inproceedings{sun2024gptscan,
    author = {Sun, Yuqiang and Wu, Daoyuan and Xue, Yue and Liu, Han and Wang, Haijun and Xu, Zhengzi and Xie, Xiaofei and Liu, Yang},
    title = {{GPTScan}: Detecting Logic Vulnerabilities in Smart Contracts by Combining GPT with Program Analysis},
    year = {2024},
    isbn = {9798400702174},
    publisher = {Association for Computing Machinery},
    address = {New York, NY, USA},
    url = {https://doi.org/10.1145/3597503.3639117},
    doi = {10.1145/3597503.3639117},
    booktitle = {Proceedings of the IEEE/ACM 46th International Conference on Software Engineering},
    articleno = {166},
    numpages = {13},
    series = {ICSE '24}
}
```
