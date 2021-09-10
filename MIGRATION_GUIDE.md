Migration Guide
====================

v5.0.1 -> 6.0.0.1
------------------
- 新的验证码识别模块依赖 opencv，安装命令 `pip3 install opencv-python`；
- 新的验证码识别模块不再依赖于 joblib，并删除了对 simplejson 的可选依赖；
- PyTorch 版本需保证大于等于 1.5.0，否则无法读取 CNN 模型。
