# PKUAutoElective_updated

北大选课网 **补退选** 阶段自动选课小工具 v7.0.0.1 (2022.02.20)

upd: 重新适配验证码，利用ddddocr库，可能存在少量读取错误问题？qwq

----------------------------------------------------------

适配2021年秋季学期北大选课网环境，目前支持 `本科生（含辅双）` 和 `研究生` 选课

再次鸣谢 [zhongxinghong/PKUAutoElective](https://github.com/zhongxinghong/PKUAutoElective) 的杰出工作，这一项目使得第一周认真听课成为了可能

## 重要注意事项

特地将一些重要的说明提前写在这里，希望能得到足够的重视。

1. 不要使用过低的刷新间隔，以免对选课网服务器造成压力。建议时间间隔不小于 4 秒。（参见`refresh_interval`的参数说明）
2. 选课网存在 IP 级别的限流，**访问过于频繁可能会导致 IP 被封禁**。单个IP最多建立5个有效的会话。（参见`elective_client_pool_size`的参数说明）
3. ***不要*** 把**同班号、有空位，但是不想选**的课放入选课计划；***不要*** 在选课网遭遇突发流量的时候**拥挤选课**。否则很可能遭遇 ***未知错误*** ！

## 从2020秋季学期v5.0.1升级
- 新的验证码识别模块依赖 `opencv`，运行命令 `pip3 install opencv-python` 以安装额外的依赖包。
- PyTorch 版本需保证**大于等于 1.5.0**，否则无法读取 CNN 模型。

## 简单配置说明

这一部分内容简化自[zhongxinghong/PKUAutoElective/README.md](https://github.com/zhongxinghong/PKUAutoElective/blob/master/README.md)，您也可以在本fork的`README_origin.md`中找到这一文件以获得详尽说明。

### 安装与环境配置

**下载PKUAutoElective** 点击右上角的 `Code -> Download ZIP` 即可下载这个项目至本地。对于 git 命令行：
```console
git clone https://github.com/Chiyun-CHU/PKUAutoElective_updated.git
```

**安装Python** 可以从 [Python 官网](https://www.python.org/) 下载并安装。本项目的开发环境为Python 3.6.8。对于Linux系统：
```console
$ apt-get install python3
```

**安装依赖的Packages** 复制并以**管理员身份**运行命令，一次性安装除PyTorch之外的运行包。示例使用**清华镜像源**进行下载加速：
```console
pip3 install requests lxml Pillow opencv-python numpy flask -i https://pypi.tuna.tsinghua.edu.cn/simple
```

**安装PyTorch** 可以从 [PyTorch 官网](https://pytorch.org/) 中选择合适的条件获得下载命令并以**管理员身份**运行安装。基本通用的示例选项：
```console
pip3 install torch torchvision torchaudio
```

### 基础配置与使用

1. 复制 `config.sample.ini` 文件，所得的新文件重命名为 `config.ini`。用文本编辑器打开 `config.ini` 
    - 直接复制文件，不要新建一个文件叫 `config.ini`，然后复制粘贴内容，否则可能会遇到编码问题。
2. 配置 `[user]`，填写`student_id`和`password`，详细见注释。
    - 替换` = `后的数据为真实值后，请妥善保管`config.ini`文件，避免泄露个人校园网账户密码。
    - 如果是双学位账号，设置 `dual_degree` 为 `true`，同时需要设置登录身份 `identity`，非双学位账号两者均保持默认即可。
3. 在选课网上，将待选课程手动添加到选课网的 `补退选` 页 `选课计划` 中。
    - 将待选课程在 `补退选` 页 `选课计划` 列表中的 **对应页数** 作为 `supply_cancel_page`的值 。
    - 该项目无法检查选课计划的合理性，请自行**确保填写的课程在有名额的时候可以被选上**，以免浪费时间。
4. 配置 `[course]` 定义待选课程，详细见注释。
    - `[course:]` 冒号后可定义唯一的课程标识符号。
    - 中间请间隔一个空白行。
    - `name` 为补退选页面中的`课程名称`。
    - `class` 为补退选页面中的`班号`。
    - `school` 为补退选页面中的`开课单位`。
5. 如果有需要使用高级功能，请务必仔细阅读详细的项目说明 [README_orgin.md](https://github.com/Chiyun-CHU/PKUAutoElective_updated/blob/master/README_origin.md)。
6. 进入项目根目录，运行 `python3 main.py`，即可开始自动选课。


## 一点额外陈述

计算中心对抗PKUAutoElective的方法相当简单粗暴，那就是每学期稍稍修改选课网的逻辑，使得程序的代码逻辑无法得到正确的请求响应而报错，破坏程序的正常工作。

除去2021年春季学期引入动态验证码的重大更新，选课网的这些小动作实际上并不涉及复杂的代码修改，基本可以在当时的补退选第一阶段开始，服务器宕机结束后的一小时到一天内完成更新。这意味着每学期选课网补退选阶段的更新，并不能支撑到选课网真正的压力峰值，即补退选第二阶段跨院系选课开始。

感谢PKUAutoElective的原作者在2021年春季几乎以一己之力标注了大量动态验证码，并完成了卷积神经网络[zhongxinghong/PKUElectiveCaptcha2021Spring](https://github.com/zhongxinghong/PKUElectiveCaptcha2021Spring)的训练，使得验证码的识别可以继续在本地进行，不再需要按照此前的解决方法调用商业性云的API，大大降低了高达几分钱的成本并节约了长达数分钟的配置时间。

此外，由于2021年春季学期更新同时引入了新的补退选第一阶段，即现在的候补选课阶段，这使得PKUAutoElective的维护者可以提前了解本学期的补退选页面逻辑，更早地对程序进行相应的更新。

然而，随着PKUAutoElective的原作者毕业离校，失去了访问选课网进行测试的权限，这一项目的维护必须由在校的更低年级学生接手，否则随着选课网的迭代，这些代码最终将变为一些无意义的文件。目前来看，至少2021年秋季学期的选课网更新，以笔者的代码水平，还能够通过对原作者代码上小修小补完成应对。

**致计算中心** 基于前面的陈述，如果计算中心想要进一步制裁PKUAutoElective以降低补退选各个阶段开始时选课网服务器的压力，请将补退选三个阶段的操作逻辑进行分离，让可用版本的发布晚于选课网的崩溃。但有一点需要着重声明，无论是原作者2021年春季学期故意在跨院系选课开始数天后才放出更新的尝试，还是2021年秋季学期候补选课开始时选课网的崩溃，都毫无疑问地证明了以下事实：PKUAutoElective并没有对选课网每学期如约而至的崩溃做出决定性的贡献。

**致反感PKUAutoElective的同学** 综上所述，即使您厌恶甚至痛恨这一项目，也请以某种意义上破坏了选课公平为理由进行讨伐，而不要再以导致选课网崩溃为帽子攻击PKUAutoElective的原作者和维护者。此外，考虑到信息科学与技术的迅速发展，无论您对于这一项目的态度如何，笔者也强烈建议您尝试一下完成PKUAutoElective的配置。即使您不愿意或者不屑于使用，配置一个开源项目也是相当有意义的一件事情：它至少可以带给您一套功能基本齐全的Python环境，并且还可以为您今后学习或工作中遇到的相似情景积累宝贵经验。

> ## 原作者[Rabbit](https://github.com/zhongxinghong)的停更说明
> 
> 感谢大家两年多来对这个项目的支持！我已经大四了，这学期结束后我将毕业，那之后我将不再更新这个项目。由于测试较为困难，这个项目一直以来基本都没有接受过 PR，如果有更改和扩充功能的需要，建议你 fork 一个自己的分支。停更后该项目不会 archive，有任何问题仍然可以在 Issue 里共同讨论，如果你想宣传自己的改版后的分支，也可以在 Issue 里分享
> 
> 小白兔 写于 2021.03.12
