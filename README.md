<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>水下组 视频压缩 技术改进评估报告</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+SC:wght@300;400;500;700&display=swap" rel="stylesheet">
    <!-- Chosen Palette: Cool Gray -->
    <!-- Application Structure Plan: The SPA is designed as a top-down exploratory narrative. It begins with a high-level introduction, followed by the core interactive element: a filterable grid of "technology cards". This allows users to immediately engage with the content and find papers relevant to their interests (e.g., only those with available code). Clicking a card opens a modal for a deep dive, preventing UI clutter. The experience concludes with a strategic synthesis—a visual roadmap combining a quantitative bubble chart (for a quick, impactful overview of priority vs. difficulty) and qualitative textual summaries. This structure was chosen to guide the user from general understanding to specific exploration and finally to strategic insight, making the dense technical report digestible and actionable. -->
    <!-- Visualization & Content Choices: 
        - Report Info: Full list of papers. Goal: Organize & Compare. Viz/Method: Interactive Card Grid with filters (HTML/Tailwind/JS). Interaction: Click filters to dynamically display relevant paper cards. Click cards to open a modal with detailed analysis. Justification: Converts a static table into an engaging, user-driven exploratory tool.
        - Report Info: Strategic Roadmap. Goal: Compare & Prioritize. Viz/Method: Bubble Chart (Chart.js/Canvas) and a 3-column layout. Interaction: Hover over bubbles for detailed tooltips. Justification: The chart visualizes the complex trade-off between impact and difficulty, offering an immediate "at-a-glance" understanding that text alone cannot provide. The column layout presents the roadmap details in a clear, parallel structure.
        - Report Info: Detailed Paper Analysis. Goal: Inform. Viz/Method: Structured text in a modal window. Interaction: Accessed by clicking a card. Justification: On-demand information access keeps the main dashboard clean and focused, improving usability.
        - CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. All visuals are rendered using Chart.js on a Canvas element or built with standard HTML and Tailwind CSS. -->
    <style>
        body {
            font-family: 'Noto Sans SC', sans-serif;
        }
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 900px;
            margin-left: auto;
            margin-right: auto;
            height: 400px;
        }
        @media (min-width: 768px) {
            .chart-container {
                height: 500px;
            }
        }
        .modal {
            transition: opacity 0.25s ease;
        }
        .modal-content {
            transition: transform 0.25s ease;
        }
        .card {
             backface-visibility: hidden;
            transform: translateZ(0);
            -webkit-font-smoothing: subpixel-antialiased;
        }
        .active-filter {
            background-color: #1D4ED8;
            color: white;
        }
        .filter-btn {
            background-color: #E0E7FF;
            color: #374151;
        }
        .filter-btn:hover {
            background-color: #C7D2FE;
        }
    </style>
</head>
<body class="bg-gray-50 text-gray-800">

    <div id="app" class="container mx-auto p-4 md:p-8">
        
        <header class="text-center mb-12">
            <h1 class="text-4xl md:text-5xl font-bold text-gray-900 mb-2">水声通信用深度学习视频压缩框架 改进技术报告</h1>
            <p class="text-lg text-gray-600">近期压缩技术创新及其应用潜力分析</p>
        </header>

        <section id="intro" class="max-w-4xl mx-auto bg-white p-8 rounded-xl shadow-md mb-12">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">报告引言</h2>
            <p class="text-gray-600 leading-relaxed">
                基于学习的视频压缩技术，特别是为实时低延迟设计的DCVC-RT模型，已成为业界焦点。尽管其性能出色，但在压缩效率、码率控制和部署复杂性上仍有提升空间。本报告系统性梳理2025cvpr（2025年5月至6月）数据压缩领域的前沿研究，聚焦于**熵模型改进**、**可变码率控制**及**其他辅助架构创新**，为DCVC-RT的演进提供清晰的技术路线图和可行的集成路径。
            </p>
        </section>

        <main>
            <div class="bg-white rounded-xl shadow-lg p-6 md:p-8 mb-12">
                <h2 class="text-2xl md:text-3xl font-bold text-gray-800 mb-6 text-center">创新点分类</h2>
                
                <div id="filters" class="flex flex-wrap justify-center gap-2 md:gap-4 mb-8">
                    <!-- Filter buttons will be injected here -->
                </div>
    
                <div id="tech-grid" class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-6">
                    <!-- Technology cards will be injected here -->
                </div>
            </div>

            <section id="roadmap" class="bg-white rounded-xl shadow-lg p-6 md:p-8">
                <h2 class="text-2xl md:text-3xl font-bold text-gray-800 mb-2 text-center">战略路线图与优先级</h2>
                <p class="text-center text-gray-500 mb-8 max-w-3xl mx-auto">
                    下图表展示各项技术的潜力影响与预估实现难度，气泡大小代表其优先级。高影响、低难度的技术是短期优选，而高影响、高难度的技术则代表了长期的大创新方向。
                </p>
                <div class="chart-container mb-12">
                    <canvas id="roadmapChart"></canvas>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-3 gap-8 text-center">
                    <div class="bg-blue-50 p-6 rounded-lg border border-blue-200">
                        <h3 class="text-xl font-bold text-blue-800 mb-3">短期路线图 (3-6个月)</h3>
                        <p class="text-blue-700">聚焦低工作量、高影响力的模块化改进、替换，优先选择已有开源代码的技术，快速提升模型实用性。</p>
                    </div>
                    <div class="bg-green-50 p-6 rounded-lg border border-green-200">
                        <h3 class="text-xl font-bold text-green-800 mb-3">中期路线图 (6-12个月)</h3>
                        <p class="text-green-700">聚焦能带来显著性能飞跃的架构级增强，深入设计与模型改进。</p>
                    </div>
                    <div class="bg-purple-50 p-6 rounded-lg border border-purple-200">
                        <h3 class="text-xl font-bold text-purple-800 mb-3">长期愿景 (12个月+)</h3>
                        <p class="text-purple-700">聚焦可能重塑整个领域的高回报的颠覆性研究，探索新一代视频压缩编解码器（提出水声通信，空天通信视频压缩模型）。</p>
                    </div>
                </div>
            </section>
        </main>

        <footer class="text-center mt-12 text-gray-500 text-sm">
            <p>&copy; 2025 技术评估报告 郑禹超. All rights reserved.</p>
            <p>基于B组 实验及论文阅读报告。 参与人：史良凡</p>
        </footer>
    </div>

    <!-- Modal Structure -->
    <div id="modal" class="modal fixed inset-0 z-50 flex items-center justify-center bg-black bg-opacity-60 p-4 hidden opacity-0">
        <div id="modal-content" class="modal-content bg-white rounded-2xl shadow-2xl w-full max-w-3xl max-h-[90vh] overflow-y-auto transform scale-95">
            <div class="sticky top-0 bg-white/80 backdrop-blur-sm z-10 flex justify-between items-center p-5 border-b border-gray-200">
                <h3 id="modal-title" class="text-xl font-bold text-gray-800"></h3>
                <button id="modal-close" class="text-gray-400 hover:text-gray-800 transition-colors">
                    <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path></svg>
                </button>
            </div>
            <div id="modal-body" class="p-6 md:p-8">
                <!-- Modal content goes here -->
            </div>
        </div>
    </div>

    <script>
    document.addEventListener('DOMContentLoaded', () => {
        const techData = [
            {
                id: '2025CVPR Wei Jiang, Junru Li, Kai Zhang, Li Zhang',
                title: 'BiECVC: Gated Diversification of Bidirectional Contexts',
                category: '熵模型改进',
                code: false,
                impact: 7, difficulty: 5, priority: 8,
                summary: '(6.17日阅读的ECVC改进)门控机制动态过滤上下文，结合非局部依赖建模。',
                problem: 'DCVC-RT的熵模型在处理遮挡或复杂运动时，无法有效评估和过滤包含噪声的时间先验，导致概率估计次优，浪费码率。',
                analysis: '引入“双向上下文门控”机制，其核心思想是根据条件编码结果动态过滤上下文信息。尽管为B帧设计，其“门控”思想可改造用于DCVC-RT的单向预测，管理时间上下文的可靠性。',
                integration: '改造为单向“时间上下文门控”模块，集成至DCVC-RT。小型网络根据时间上下文和当前帧特征生成调制图，逐元素乘到时间上下文上，端到端训练使其学会“关断”不可靠区域的先验影响。',
                value: '实现了从“被动接收”到“主动验证”的转变。通过移植B帧中解决上下文冲突的门控方案来管理P帧中单个上下文的可靠性，能有效抑制误差传播，提升压缩性能。'
            },
            {
                id: '2025CVPR Xihua Sheng, Peilin Chen, Meng Wang, Li Zhang, Shiqi Wang, Dapeng Oliver Wu',
                title: 'Fine-Grained Motion Compression and Selective Temporal Fusion',
                category: '熵模型改进',
                code: false,
                impact: 6, difficulty: 6, priority: 7,
                summary: '交互式熵模型，利用已解码分段作为方向先验。',
                problem: '标准的自回归模型采用简单的光栅扫描顺序，其因果依赖特性限制了捕捉复杂、非局部空间关联的能力。',
                analysis: '提出“交互式运动熵模型”，将潜在表示划分，利用已解码部分作为后续部分解码的强“方向性先验”，提供比传统因果模型更丰富的上下文。',
                integration: '将此“交互式”概念从运动域迁移到残差潜在表示的空间域。将熵模型重构为两遍系统：解码上半部分后，将其作为丰富的非因果先验来指导下半部分的解码。',
                value: 'B帧研究孵化出高级上下文建模技术。其“上下文A与B交互”的本质可被迁移，利用空间对称性而非时间对称性，为改进空间自回归模型提供了深刻的技术洞察。'
            },
            {
                id: '2025 Zijian Liang, Kai Niu, Changshuo Wang, Jin Xu, Ping Zhang',
                title: 'Synonymous Variational Inference for Perceptual Image Compression',
                category: '熵模型改进',
                code: true,
                impact: 9, difficulty: 9, priority: 9,
                summary: '提出“同义变分推断”理论，为率失真感知（R-D-P）权衡提供数学框架。',
                problem: '当前编解码器普遍使用的率-失真-感知（R-D-P）三元权衡在理论上是经验性的，缺乏坚实的数学基础来指导优化。',
                analysis: '引入基于语义信息论的全新理论框架，定义了由所有感知上相似图像构成的“同义集”（Synset），推导出“同义率-失真-感知权衡”的优化目标。',
                integration: '作为长期战略指导，重新审视DCVC-RT的损失函数。其理论为加入GAN或扩散损失等分布匹配项提供了形式化的合理解释，可指导对损失项权重进行更具原则性的调整。',
                value: '从经验技巧到理论原则的深刻转变。它将压缩目标从“精确重建像素”重新定义为“重建任何一张属于同一感知同义集的图像”，为生成式压缩提供了坚实的理论基石。'
            },
            {
                id: '2025CVPR Sang NguyenQuang, Cheng-Wei Chen, Xiem HoangVan, Wen-Hsiao Peng',
                title: 'A Rate-Quality Model for Learned Video Coding',
                category: '可变码率控制',
                code: false,
                impact: 7, difficulty: 4, priority: 9,
                summary: '提出RQNet，一个用于预测码率-质量关系的神经网络模型。',
                problem: '学习型编码器缺乏准确的R-Q模型来智能选择质量参数以匹配目标码率，导致码率控制不准或依赖耗时的多遍编码。',
                analysis: '提出RQNet，一个专门预测R-Q关系的轻量级神经网络，并设计了在线自适应机制，利用已编码帧的数据动态更新模型参数，提升预测精度。',
                integration: '作为独立码率控制模块集成到DCVC-RT编码器。用预训练的RQNet预测初始质量参数，并用每帧编码后的实际数据在线微调模型，实现精确的比特分配。',
                value: '经典控制理论与深度学习的融合。用数据驱动的学习方法取代传统手工设计的解析模型，是解决深度学习模型R-Q行为复杂性的一种相对保守但极为实用的策略。'
            },
            {
                id: '2025CVPR Hyunmo Yang, Seungjun Oh, Eunbyung Park',
                title: 'Parameter-Efficient Instance-Adaptive Neural Video Compression',
                category: '可变码率控制',
                code: true,
                impact: 9, difficulty: 6, priority: 10,
                summary: '应用LoRA等参数高效微调（PEFT）技术实现对特定视频的实例自适应压缩。',
                problem: '预训练的“平均”模型无法对特定视频达到最优性能，而对每个视频都进行完整微调在计算上不可行。',
                analysis: '将LoRA等PEFT技术适配到视频压缩任务。通过在预训练解码器中插入轻量级“适配器”并只微调这些模块，用极小的计算和码率开销实现了接近完整微调的性能增益。',
                integration: '在DCVC-RT解码器中插入LoRA适配器。编码新视频时，快速微调适配器参数，并将更新后的少量权重作为边信息传输，使解码器“变身”为专门优化的模型。',
                value: '从根本上改变了模型专业化的经济学，使其变得“廉价”。编码器演进为能持续学习和自适应的动态系统，是弥合学习型与传统编解码器性能差距的关键一步。'
            },
            {
                id: '2025CVPR Jinpei Guo, Yifei Ji, Zheng Chen, Kai Liu, Min Liu, Wang Rao, Wenbo Li, Yong Guo, Yulun Zhang',
                title: 'OSCAR: One-Step Diffusion Codec Across Multiple Bit-rates',
                category: '可变码率控制',
                code: true,
                impact: 10, difficulty: 10, priority: 10,
                summary: '将码率映射为伪扩散时间步，实现单一模型支持多码率的单步生成式解码。',
                problem: '可变码率控制的终极目标是单一模型支持任意码率，而非依赖为每个码率点训练模型的变通方法。',
                analysis: '革命性地将量化的潜在表示视为原始表示的“带噪”版本，从而将码率映射到“伪扩散时间步”。基于此，训练一个以时间步为条件的单步生成式扩散模型作为解码器。',
                integration: '用一个以码率（伪时间步）为条件的单步扩散模型替换DCVC-RT的合成网络（解码器）。只需向解码器提供不同的伪时间步值，即可生成对应码率的重建。',
                value: '范式级转变。在量化（信息移除）和生成（解码）间建立了深刻联系，量化等价于扩散的前向加噪。这种优雅的统一自然地导出了单一、多码率的通用解码器模型。'
            },
            {
                id: '2025CVPR Chuanbo Tang, Zhuoyuan Li, Yifan Bian, Li Li, Dong Liu',
                title: 'Neural Video Compression with Context Modulation',
                category: '辅助架构创新',
                code: false,
                impact: 8, difficulty: 6, priority: 8,
                summary: '通过光流朝向和上下文补偿主动调制时间上下文，消除无关信息。',
                problem: '简单的时间上下文传播在遇到大运动、遮挡等复杂场景时会失效，导致预测不准，残差编码消耗大量码率。',
                analysis: '提出对时间上下文进行主动“调制”，引入“光流朝向”挖掘额外信息，并通过“上下文补偿”模块进行融合，旨在消除无关信息，抑制误差传播。',
                integration: '将上下文调制模块直接集成到DCVC-RT的时间上下文传播路径中，在传播的特征被使用前对其进行精炼和修正，是对模型核心机制的直接增强。',
                value: '标志着从“被动传播”到对时间上下文进行“主动智能化管理”的趋势转变，将上下文传播模块从简单的“内存缓冲区”演进为动态的、具备自我修正能力的“预测引擎”。'
            },
            {
                id: 'CVPR2025 Yifan Bian, Chuanbo Tang, Li Li, Dong Liu',
                title: 'Augmented Deep Contexts for Spatially Embedded Video Coding',
                category: '辅助架构创新',
                code: true,
                impact: 6, difficulty: 7, priority: 6,
                summary: '采用可伸缩编码思想，压缩低分辨率视频作为额外的空间参考。',
                problem: '在场景切换等时间参考完全失效的情况下，模型缺乏鲁棒的后备方案，导致性能急剧下降。',
                analysis: '引入可伸缩编码思想，通过一个轻量级编码器压缩一个低分辨率版本的视频，并将其重建结果作为额外的空间参考，提供“安全网”。',
                integration: '为DCVC-RT增加一个轻量级基准层编解码器。其输出的低分辨率重建帧经上采样后，作为额外输入送入主合成网络，以处理场景切换等困难情况。',
                value: '同样体现了主动管理上下文的思想。通过增加空间维度的参考，为时间预测失效的极端情况提供了鲁棒的补充信息，增强了模型的泛化能力。'
            },
            {
                id: '2506.01229',
                title: 'Structured Pruning and Quantization for Learned Image Compression',
                category: '辅助架构创新',
                code: true,
                impact: 8, difficulty: 5, priority: 9,
                summary: '基于率失真损失的神经架构搜索（NAS）进行结构化剪枝。',
                problem: '通用的模型压缩技术没有考虑视频压缩中特有的率失真（R-D）目标，导致优化效果次优，影响实时性能。',
                analysis: '提出一种结构化剪枝方法，利用神经架构搜索（NAS）来自动寻找每一层网络的最优剪枝率，而搜索的优化目标直接就是R-D损失函数。',
                integration: '应用此方法对DCVC-RT网络进行剪枝。这可以作为优化工作流的第一步，创造出一个更小、更快的DCVC-RT网络架构，为后续量化做准备。',
                value: '学习型编解码器“工业化”的标志。专门的、R-D感知的模型压缩技术是原型走向实际部署的必要条件，是兑现DCVC-RT“实时”承诺的关键工程任务。'
            },
            {
                id: 'ICME 2024 Md Adnan Faisal Hossain, Zhihao Duan, Fengqing Zhu',
                title: 'Flexible Mixed Precision Quantization for Learned Image Compression',
                category: '辅助架构创新',
                code: true,
                impact: 8, difficulty: 5, priority: 9,
                summary: '基于率失真损失敏感度进行灵活的混合精度量化。',
                problem: '统一的量化策略（如全部INT8）并非最优，不同网络层对量化精度的敏感度不同，尤其是在R-D性能上。',
                analysis: '根据网络中不同层对R-D损失的敏感度，为其分配不同的量化比特宽度（如INT8/INT4），并通过高效搜索算法找到最优的混合精度分配方案。',
                integration: '在`2506.01229`剪枝后的DCVC-RT模型上应用此混合精度量化方法。进一步优化模型以实现硬件加速，显著减小模型尺寸并提升推理速度。',
                value: '与R-D剪枝一脉相承，是模型工业化部署的另一关键环节。通过软硬件协同设计，最大化利用硬件特性，将学习型编解码器的效率推向极致。'
            }
        ];
        
        const filtersContainer = document.getElementById('filters');
        const grid = document.getElementById('tech-grid');
        const modal = document.getElementById('modal');
        const modalContent = document.getElementById('modal-content');
        const modalTitle = document.getElementById('modal-title');
        const modalBody = document.getElementById('modal-body');
        const modalClose = document.getElementById('modal-close');

        let currentFilter = 'all';

        const filterCategories = {
            'all': '全部技术',
            '熵模型改进': '熵模型改进',
            '可变码率控制': '可变码率控制',
            '辅助架构创新': '辅助架构创新',
            'code': '代码可用'
        };

        function renderFilters() {
            filtersContainer.innerHTML = Object.entries(filterCategories).map(([key, value]) => `
                <button data-filter="${key}" class="filter-btn text-sm md:text-base font-semibold py-2 px-5 rounded-full transition-colors duration-200 ${key === currentFilter ? 'active-filter' : ''}">
                    ${value}
                </button>
            `).join('');
        }

        function renderCards() {
            const filteredData = techData.filter(item => {
                if (currentFilter === 'all') return true;
                if (currentFilter === 'code') return item.code;
                return item.category === currentFilter;
            });

            grid.innerHTML = filteredData.map(item => `
                <div class="card bg-white p-6 rounded-xl border border-gray-200 hover:shadow-2xl hover:-translate-y-1 transition-all duration-300 cursor-pointer flex flex-col justify-between" data-id="${item.id}">
                    <div>
                        <div class="flex justify-between items-start mb-3">
                             <span class="text-xs font-semibold ${
                                 {'熵模型改进': 'bg-red-100 text-red-800', '可变码率控制': 'bg-blue-100 text-blue-800', '辅助架构创新': 'bg-green-100 text-green-800'}[item.category]
                             } py-1 px-3 rounded-full">${item.category}</span>
                            ${item.code ? '<span class="text-xs font-bold text-green-600">✓ 代码可用</span>' : '<span class="text-xs text-gray-400">代码不可用</span>'}
                        </div>
                        <h3 class="text-lg font-bold text-gray-900 mb-2">${item.title}</h3>
                        <p class="text-gray-600 text-sm">${item.summary}</p>
                    </div>
                    <div class="text-right mt-4">
                        <span class="text-sm font-mono text-gray-400">ID: ${item.id}</span>
                    </div>
                </div>
            `).join('');
        }

        function showModal(id) {
            const item = techData.find(d => d.id === id);
            if (!item) return;

            modalTitle.textContent = item.title;
            modalBody.innerHTML = `
                <div class="space-y-6">
                    <div>
                        <h4 class="font-bold text-lg text-gray-800 mb-2 border-l-4 border-red-500 pl-3">问题陈述</h4>
                        <p class="text-gray-600 leading-relaxed">${item.problem}</p>
                    </div>
                    <div>
                        <h4 class="font-bold text-lg text-gray-800 mb-2 border-l-4 border-blue-500 pl-3">相关研究分析</h4>
                        <p class="text-gray-600 leading-relaxed">${item.analysis}</p>
                    </div>
                    <div>
                        <h4 class="font-bold text-lg text-gray-800 mb-2 border-l-4 border-green-500 pl-3">面向DCVC-RT的集成路径</h4>
                        <p class="text-gray-600 leading-relaxed">${item.integration}</p>
                    </div>
                     <div>
                        <h4 class="font-bold text-lg text-gray-800 mb-2 border-l-4 border-purple-500 pl-3">深层价值剖析</h4>
                        <p class="text-gray-600 leading-relaxed">${item.value}</p>
                    </div>
                </div>
            `;
            modal.classList.remove('hidden');
            document.body.style.overflow = 'hidden';
            setTimeout(() => {
                modal.classList.remove('opacity-0');
                modalContent.classList.remove('scale-95');
            }, 10);
        }

        function hideModal() {
            modal.classList.add('opacity-0');
            modalContent.classList.add('scale-95');
            document.body.style.overflow = '';
            setTimeout(() => {
                modal.classList.add('hidden');
            }, 250);
        }

        filtersContainer.addEventListener('click', e => {
            if (e.target.tagName === 'BUTTON') {
                currentFilter = e.target.dataset.filter;
                renderFilters();
                renderCards();
            }
        });

        grid.addEventListener('click', e => {
            const card = e.target.closest('.card');
            if (card) {
                showModal(card.dataset.id);
            }
        });

        modalClose.addEventListener('click', hideModal);
        modal.addEventListener('click', e => {
            if (e.target === modal) {
                hideModal();
            }
        });
        
        function renderRoadmapChart() {
            const ctx = document.getElementById('roadmapChart').getContext('2d');
            const chartData = {
                datasets: [{
                    label: '短期 (6-12个月)',
                    data: techData.filter(d => ['2505.02720', '2506.01229', '2506.01221', '2505.14541'].includes(d.id)).map(d => ({ x: d.difficulty, y: d.impact, r: d.priority })),
                    backgroundColor: 'rgba(59, 130, 246, 0.6)',
                    borderColor: 'rgba(37, 99, 235, 1)',
                    borderWidth: 2
                }, {
                    label: '中期 (12-24个月)',
                    data: techData.filter(d => ['2405.08530', '2506.07709', '2505.09193'].includes(d.id)).map(d => ({ x: d.difficulty, y: d.impact, r: d.priority })),
                    backgroundColor: 'rgba(16, 185, 129, 0.6)',
                    borderColor: 'rgba(5, 150, 105, 1)',
                    borderWidth: 2
                }, {
                    label: '长期 (24个月+)',
                    data: techData.filter(d => ['2505.16091', '2505.22438'].includes(d.id)).map(d => ({ x: d.difficulty, y: d.impact, r: d.priority })),
                    backgroundColor: 'rgba(168, 85, 247, 0.6)',
                    borderColor: 'rgba(147, 51, 234, 1)',
                    borderWidth: 2
                }]
            };

            new Chart(ctx, {
                type: 'bubble',
                data: chartData,
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            position: 'bottom',
                            labels: {
                                padding: 20,
                                font: {
                                    size: 14
                                }
                            }
                        },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    const item = techData.find(d => d.difficulty === context.parsed.x && d.impact === context.parsed.y);
                                    if (item) {
                                        return `${item.title}: (难度: ${item.difficulty}, 影响: ${item.impact}, 优先级: ${item.priority})`;
                                    }
                                    return '';
                                }
                            },
                            backgroundColor: '#111827',
                            titleFont: { size: 16 },
                            bodyFont: { size: 12 },
                            padding: 12,
                            cornerRadius: 8,
                            displayColors: false
                        }
                    },
                    scales: {
                        x: {
                            title: {
                                display: true,
                                text: '预估实现难度 (低 → 高)',
                                font: {
                                    size: 16,
                                    weight: 'bold'
                                },
                                padding: {top: 10}
                            },
                             min: 3,
                             max: 11
                        },
                        y: {
                            title: {
                                display: true,
                                text: '潜力影响/价值 (低 → 高)',
                                font: {
                                    size: 16,
                                    weight: 'bold'
                                },
                                 padding: {bottom: 10}
                            },
                             min: 5,
                             max: 11
                        }
                    }
                }
            });
        }

        renderFilters();
        renderCards();
        renderRoadmapChart();
    });
    </script>
</body>
</html>
