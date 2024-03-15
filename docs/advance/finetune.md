## Fine tuning with LoRa

> 💡 如果你打算用 LoRa 对 RWKV 进行微调，首先得明确自己在做什么。
> 
> 如果你对 RWKV 不熟悉，建议先玩玩基础模型，再尝试用 LoRa 进行微调。
> 
> 在很多情况下，大多数人想要实现的目标可以通过调整他们的提示词（prompts）来完成，这比微调要简单得多。

完整的 LoRa 微调指南，可以在以下位置找到：
- [https://mysymphony.jp.net/a/rwkv-character/](https://mysymphony.jp.net/a/rwkv-character/)
- [https://zhuanlan.zhihu.com/p/638326262 (知乎)](https://zhuanlan.zhihu.com/p/638326262)

对于使用 LoRa 的微调，请使用以下任一 LoRa 实现：
- [https://github.com/m8than/RWKV-LM-LoRA](https://github.com/m8than/RWKV-LM-LoRA)
- [https://github.com/Blealtan/RWKV-LM-LoRA](https://github.com/Blealtan/RWKV-LM-LoRA)

为了获得最佳性能，建议首先将你的数据集转换为二进制格式：
- [https://github.com/Abel2076/json2binidx_tool](https://github.com/Abel2076/json2binidx_tool)

> 大多数现有的微调脚本，只要你使用 json2binidx 工具预先对你的数据集进行 token 化，就应该能与 World 模型一起工作。

## 不使用 LoRa 的微调

另外，你可以使用官方仓库，不使用 LoRa（或上述 LoRa 项目，不使用 LoRa 标志）来微调项目：
- [https://github.com/BlinkDL/RWKV-LM/tree/main/RWKV-v4neo](https://github.com/BlinkDL/RWKV-LM/tree/main/RWKV-v4neo)

或者使用 infctx 训练器进行微调：
- [https://github.com/RWKV/RWKV-infctx-trainer/](https://github.com/RWKV/RWKV-infctx-trainer/)

一般来说，使用 BlinkDL 的训练器可以获得更好的原始性能，使用 infctx 可以牺牲一些速度换取 infctx 大小的支持。

## 从头开始训练模型？

参考主项目：
- [https://github.com/BlinkDL/RWKV-LM/](https://github.com/BlinkDL/RWKV-LM/)

## 需要多少 GPU vRAM？

以下是微调 RWKV 所需最小 GPU vram 的粗略估计：
- **1.5b** : 15GB
- **3b** : 24GB
- **7b** : 48GB
- **14b** : 80GB

注意，如果你希望微调快速且稳定，可能需要更多内存。

使用 LoRa & DeepSpeed，你也许可以用 1/2 或更少的 vram 要求来完成。

## 解决 Python 依赖问题

如果你遇到 Python 依赖问题，可以尝试以下方法在 Ubuntu 20.04 的 AWS 实例上进行安装：

```bash
# 确保安装了 haveged, ninja 和 python 本身
sudo apt-get install -y haveged ninja-build python3-pip python-is-python3

# 检查 nvidia-smi 是否工作
nvidia-smi

# 如果 nvidia SMI 不工作：你需要以下步骤来安装 nvidia 驱动
# sudo apt install -y  nvidia-driver-515 nvidia-dkms-515

# 如果没有安装 conda，请在线查看其安装说明
# https://www.anaconda.com/download#downloads

# 更新 conda
conda update conda

# 你可能需要重新初始化你的 shell
conda init bash

# 创建并激活 conda 环境
conda create -y --name rwkv_4neo python=3.10
conda activate rwkv_4neo

# 安装 cuda 工具包
conda install -y -c conda-forge cudatoolkit=11.7 cudatoolkit-dev=11.7 

# 特别安装带有 cuda 1.17 的 pytorch 1.13.1
python -m pip install torch==1.13.1+cu117 torchvision==0.14.1+cu117 torchaudio==0.13.1 --extra-index-url https://download.pytorch.org/whl/cu117
python -m pip install deepspeed==0.7.0 pytorch-lightning==1.9
python -m pip install ninja wandb transformers
```
