# GPTScan——《区块链金融安全》期末大作业代码复现

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

## 如何引用本项目

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
