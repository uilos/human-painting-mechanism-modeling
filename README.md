# human-painting-mechanism-modeling

## Overview
A computational framework for modeling human painting as process, action, material, and judgment.

## Research Motivation
当下以图像结果层面的统计关联为核心的AI生成图像技术，在纯艺术绘画领域的应用和发展，存在结构性限制。表现在画面元素在视觉特征的相关性层面被考察，而非因果性，导致没有内部关系连续性的画面元素会被强行组织在一起，形成视觉风格相似但内部造型关系错误、难以通过人类专家检验的，所谓“AI式绘画”。

## Core Idea
绘画的视觉表现不是对视觉的满足，而是对以视觉为信息入口的身体的满足。

## Framework
```mermaid
flowchart TD
    A[Experience / Need] --> B[Target Model]
    B --> C[Target Experience State T]

    D[Current Canvas C_t] --> E[Canvas State Encoder]
    E --> F[Canvas State S_t]

    C --> G[Constraint Evaluator]
    F --> G
    G --> H[Diagnosis / Gap E_t]

    H --> I[Action Generator / Policy]
    F --> I
    J[Painter Strategy θ] --> I
    K[Constraint Library] --> G
    K --> I

    I --> L[Candidate Actions]
    L --> M[Counterfactual Simulator]
    M --> N[Selected Action a_t]

    N --> O[Material-Trace Renderer]
    D --> O
    O --> P[Updated Canvas C_t+1]

    P --> E

    H --> Q[Revision Model]
    Q --> I

    F --> R[Stop Model]
    C --> R
    S[Painting History H_t] --> R
    R -->|Continue| I
    R -->|Stop| T2[Finished Painting]

    N --> S
    H --> S
```

```mermaid
flowchart TD
    EXP[经验 / 需求输入] --> N1
    PROMPT[语言化目标 / Prompt] --> N1
    PRE[前语言冲动<br/>身体-视觉需求] --> N2

    subgraph M1["模块1：绘画需求与目标状态模块"]
        N1[语义目标解析器] --> N2[前语言目标映射器]
        N2 --> N3[负约束生成器]
        N3 --> N4[目标状态生成器<br/>Target State T]
    end

    CANVAS[当前画布 C_t] --> S1

    subgraph M2["模块2：画面状态编码模块"]
        S1[画布读取器] --> S2[几何编码器]
        S1 --> S3[视觉重量分析器]
        S1 --> S4[笔触 / 痕迹解析器]
        S1 --> S5[材料状态解析器]
        S1 --> S6[造型结构解析器]
        S1 --> S7[画面组织解析器]
        S1 --> S8[经验状态读取器]
        S2 --> S9[画面状态汇总器<br/>Canvas State S_t]
        S3 --> S9
        S4 --> S9
        S5 --> S9
        S6 --> S9
        S7 --> S9
        S8 --> S9
    end

    N4 --> E1
    S9 --> E1

    subgraph M3["模块3：绘画约束评价模块"]
        E1[目标-状态比较器] --> E2[动作痕迹评价器]
        E1 --> E3[材料因果评价器]
        E1 --> E4[造型结构评价器]
        E1 --> E5[画面组织评价器]
        E1 --> E6[目标匹配评价器]
        E2 --> E7[失败类型分类器]
        E3 --> E7
        E4 --> E7
        E5 --> E7
        E6 --> E7
        E7 --> E8[诊断生成器<br/>Diagnosis E_t]
    end

    E8 --> A1
    S9 --> A1

    subgraph M4["模块4：绘画动作生成模块"]
        A1[动作空间生成器] --> A2[动作参数化器]
        A2 --> A3[候选动作提出器]
        A3 --> A4[策略过滤器]
        A4 --> A5[动作可行性检查器]
        A5 --> A6[候选动作集合]
    end

    A6 --> CFS1
    CANVAS --> CFS1
    S9 --> CFS1

    subgraph M5["模块5：反事实模拟模块"]
        CFS1[候选动作模拟器] --> CFS2[后果重编码器]
        CFS2 --> CFS3[收益-风险估计器]
        CFS3 --> CFS4[动作排序器]
        CFS4 --> CFS5[选定动作 a_t]
    end

    CFS5 --> R1
    CANVAS --> R1

    subgraph M6["模块6：动作—材料痕迹生成模块"]
        R1[工具模型器] --> R2[动作模型器]
        R2 --> R3[材料模型器]
        R3 --> R4[痕迹渲染器]
        R4 --> R5[层与历史处理器]
        R5 --> R6[新画布 C_t+1]
    end

    R6 --> CANVAS

    E8 --> RV1
    S9 --> RV1

    subgraph M7["模块7：修正策略模块"]
        RV1[错误-动作映射器] --> RV2[修正规划器]
        RV2 --> RV3[破坏风险检查器]
        RV3 --> RV4[目标更新器]
        RV4 --> RV5[修正计划生成器]
    end

    RV5 --> A1
    RV4 --> N4

    N4 --> ST1
    S9 --> ST1
    E8 --> ST1
    CFS3 --> ST1

    subgraph M8["模块8：停止判断模块"]
        ST1[完成度估计器] --> ST2[过度加工风险估计器]
        ST2 --> ST3[边际收益估计器]
        ST3 --> ST4{停止决策器}
    end

    ST4 -->|继续| A1
    ST4 -->|停止| FIN[完成画面]

    subgraph M9["支撑模块A：绘画约束库"]
        CL1[动作痕迹约束库] --> CL8[约束调度器]
        CL2[材料因果约束库] --> CL8
        CL3[视觉知觉约束库] --> CL8
        CL4[造型结构约束库] --> CL8
        CL5[画面组织约束库] --> CL8
        CL6[时间过程约束库] --> CL8
        CL7[传统语言约束库] --> CL8
    end

    CL8 --> E1
    CL8 --> A5
    CL8 --> ST1

    subgraph M10["支撑模块B：画家策略模型"]
        PS1[经验压缩参数器] --> PS6[策略参数汇总器 θ]
        PS2[动作偏好参数器] --> PS6
        PS3[材料偏好参数器] --> PS6
        PS4[构图倾向参数器] --> PS6
        PS5[停止倾向参数器] --> PS6
    end

    PS6 --> N2
    PS6 --> A4
    PS6 --> RV2
    PS6 --> ST1

    subgraph M11["支撑模块C：绘画历史记录模块"]
        H1[动作记录器] --> H4[历史汇总器 H_t]
        H2[状态记录器] --> H4
        H3[诊断记录器] --> H4
        H5[材料层记录器] --> H4
    end

    CFS5 --> H1
    R6 --> H2
    E8 --> H3
    R5 --> H5
    H4 --> RV1
    H4 --> ST1
    H4 --> CFS1

    subgraph M12["支撑模块D：专家反馈与验证模块"]
        EX1[专家标注器] --> EX2[失败样本整理器]
        EX2 --> EX3[判断权重校准器]
        EX3 --> EX4[验证报告生成器]
    end

    EX3 -.校准.-> CL8
    EX3 -.校准.-> E1
    EX3 -.校准.-> PS6
```
## Modules
列出 Target Model、Canvas State、Action Model、Material Function、Evaluator、Stop Model。

## Current Prototypes
列出 balance analyzer 和 brush action model。

## Roadmap
v0.1 / v0.2 / v0.3。

## Status
说明 early-stage research prototype。
