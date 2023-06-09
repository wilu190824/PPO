# PPO伪代码
初始化策略网络 π_θ ，价值网络 V_φ
初始化超参数：迭代次数 K，每次迭代的样本数 M，更新步长 ε，剪切比率 c，剪切幅度 ϵ

对于每个迭代次数 k = 1, 2, ..., K do:
    生成样本集 D = {(s_t, a_t, r_t, s_{t+1}, done)}，使用策略网络π_θ与环境交互并收集数据
    
    对于每个样本 (s_t, a_t, r_t, s_{t+1}, done) in D do:
        计算优势估计 A = 计算优势函数(s_t, r_t, s_{t+1}, done)
    
    更新策略网络：
    对于每个迭代次数 i = 1, 2, ..., I do:
        计算策略梯度：
        J = 0
        对于每个样本 (s_t, a_t, r_t, s_{t+1}, done, A) in D do:
            计算当前策略的概率分布 π_θ(a_t|s_t)
            计算旧策略的概率分布 π_old(a_t|s_t)
            计算概率比率 r_t(θ) = π_θ(a_t|s_t) / π_old(a_t|s_t)
            计算修剪项：
            L_CLIP = min(r_t(θ) * A, clip(r_t(θ), 1 - ϵ, 1 + ϵ) * A)
            J += max(L_CLIP, -c * A)
        对于每个样本计算的策略梯度的平均值：J /= |D|
        使用梯度上升更新策略网络的参数：θ = θ + ε * ∇θ J
    
    更新价值网络：
    对于每个样本 (s_t, a_t, r_t, s_{t+1}, done) in D do:
        计算当前策略的概率分布 π_θ(a_t|s_t)
        计算值函数 V_φ(s_t)
        计算优势估计 A = r_t + γ * (1 - done) * V_φ(s_{t+1}) - V_φ(s_t)
        使用梯度下降更新价值网络的参数：φ = φ + ε * ∇φ L(0.5 * (V_φ(s_t) - (r_t + γ * (1 - done) * V_φ(s_{t+1})))^2)
