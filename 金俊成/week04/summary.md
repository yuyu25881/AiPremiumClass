
1. **全连接神经网络模型结构补全**
    - **激活函数**
        - **Sigmoid**：1943年提出，输出范围为[0,1]，具有非线性特性，但存在梯度消失和输出均值不为零的问题，适用于早期分类任务和GANs输出层。
        - **Tanh**：1940年代提出，输出范围[-1,1]，均值为零，比Sigmoid易训练，但仍有梯度消失问题，常用于RNN、LSTM的门控机制。
        - **ReLU**：2012年提出，计算高效，缓解梯度消失，但存在“神经元死亡”现象，广泛应用于CNN、ResNet等。
        - **Leaky ReLU**：2013年提出，改进ReLU的“死亡”问题，输入负时保留小梯度，适用于DCGAN等。
        - **Swish**：2017年提出，公式为Swish(x)=x·σ(x)，结合平滑性和自门控机制，适合深度网络，如EfficientNet。
    - **归一化**
        - **BatchNorm**：由Sergey Ioffe和Christian Szegedy在2015年提出，对每一批次数据归一化，使其均值为0、方差为1，减少内部协变量偏移。步骤包括计算均值、方差、归一化、缩放和平移。作用是加速训练收敛、提高模型泛化能力、缓解梯度消失和爆炸。PyTorch示例代码展示了如何在全连接网络中使用BatchNorm1d。
        - **其他归一化方法**：LayerNorm适合长序列数据或批量较小的任务；GroupNorm平衡BN和LN，适合中等批量场景；SwitchableNorm自适应选择归一化方式，适合动态批量场景。
    - **正则化**
        - **Dropout**：由Geoffrey Hinton等人在2012年提出，训练时随机“关闭”部分神经元，测试时缩放补偿。作用是减少过拟合、近似模型集成、计算效率高。PyTorch示例代码展示了如何在网络中添加Dropout层，设置p=0.5表示随机失活50%的神经元。
2. **优化器**
    - **梯度下降系列**
        - **GD**：每次使用所有样本更新参数，训练速度慢，容易陷入局部最优。
        - **BGD**：与GD相同，每一次训练迭代使用所有样本更新参数，理想状态可达全局最优，但不适合大样本和大模型。
        - **MBGD**：每次使用小批量样本更新参数，训练速度比GD快，批次大小影响模型训练加速。
        - **SGD**：每次随机选择一个样本更新参数，结合MBGD，是深度学习中最基本的优化器，全称随机小批量梯度下降。
        - **带动量的SGD**：引入动量机制，将历史梯度信息加入当前梯度更新，公式为V_t=βV_{t-1}+ΔW_t，W_{t+1}=W_t−ηV_t，β通常取0.9、0.99或0.5，可加速收敛并可能逃离局部最小值。
        - **Nesterov动量**：Momentum的变种，先用当前速度临时更新参数，再用更新后的参数计算梯度，可理解为在Momentum中添加校正因子，在凸批量梯度下改进误差收敛率。
    - **自适应优化器**
        - **RMSProp**：保持梯度平方的移动平均，用该平均值调整学习率，公式为v_t=αv_{t-1}+(1−α)g_t²，参数更新为θ_t=θ_{t-1}−γg_t/√(v_t+ε)，适合RNN网络。
        - **Adam**：通过计算梯度的一阶矩和二阶矩估计调整学习率，公式为m_t=β1m_{t-1}+(1−β1)g_t，v_t=β2v_{t-1}+(1−β2)g_t²，修正偏差后参数更新为θ_t=θ_{t-1}−γm_t/(√v_t+ε)，自适应调整步长，稳定训练过程。
        - **AdamW**：在Adam基础上，对参数加入权重衰减项，将权重衰减和梯度更新分开操作，公式为θ_{t+1}=θ_t−learning_rate·(m_t/(√v_t+ε)+λ·θ_t)，避免权重衰减与学习率关联。
3. **神经网络输出层与模型任务**
    - **输出层结构**：回归任务通常使用单一输出节点，分类任务使用多输出节点，输出节点值通过softmax转换为概率分布，且概率之和为1。
    - **回归损失函数**
        - **MSE**：均方误差，公式为MSE=1/nΣ(y_i−ŷ_i)²，计算简单，放大较大误差影响，是回归问题常用损失函数。
        - **MAE**：平均绝对误差，公式为MAE=1/nΣ|y_i−ŷ_i|，对异常值鲁棒性强，但可导性不如MSE。
        - **Huber损失**：结合MSE和MAE优点，误差较小时类似MSE，误差较大时类似MAE，公式为L(y,ŷ)=0.5(y−ŷ)²（|y−ŷ|≤δ），L(y,ŷ)=δ|y−ŷ|−0.5δ²（|y−ŷ|>δ），δ为超参数。
    - **分类损失函数**
        - **交叉熵**：衡量真实分布与预测分布的差异，公式为H(p,q)=−Σp(x_i)log(q(x_i))。
        - **KL散度**：相对熵，衡量两个分布差异，公式为D_KL(p||q)=Σp(x_i)log(p(x_i)/q(x_i))，值越小分布越接近。
        - **CrossEntropyLoss**：PyTorch中用于二分类或多分类，自动调用log_softmax和NLLLoss计算，公式为loss(x,class)=−log(exp(x[class])/Σexp(x[j]))。
        - **binary_cross_entropy**：二分类交叉熵，是多分类的特殊情况，PyTorch中的BCELoss和BCEWithLogitsLoss可用于计算。
4. **Kaggle学习及模型训练资源**
    - **注册Kaggle账号**：点击首页右上角Register按钮，使用Email注册，填写相关信息并完成人机身份验证和邮箱激活。
    - **上传数据集**：登录后点击左侧Datasets选项，选择+New Dataset，添加标题并上传数据文件，点击Create后即可在代码中加载使用。
    - **编写代码并训练模型**：点击左侧Code选项，选择+New Notebook，点击右上角+Add data绑定数据集。在右侧Settings选项中配置GPU加速（需手机验证），编写代码时注意Kaggle目录结构，输入数据在/kaggle/input，输出文件在/kaggle/working。
    - **模型保存后下载**：使用torch.save()保存模型文件，在右侧data选项卡中找到模型文件，选择More action后点击Download下载。