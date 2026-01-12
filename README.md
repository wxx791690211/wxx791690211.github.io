# wxx791690211.github.io
1
[Uploading 比分控制完美版5.html…]()
<!DOCTYPE html>
<html lang="zh-CN"> <!-- 全局中文语言设置，提升输入法识别 -->
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>篮球计分板-OBS直播专用版</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* 核心动画：出场（弹入显示）+ 弹出（弹出隐藏） */
        @keyframes slideInUp { 
            0% { opacity: 0; transform: translateY(50px); } 
            100% { opacity: 1; transform: translateY(0); } 
        }
        @keyframes popOut { 
            0% { opacity: 1; transform: scale(1); } 
            70% { transform: scale(1.05); } 
            100% { opacity: 0; transform: scale(0.8); } 
        }
        .animate-slide { animation: slideInUp 0.8s ease-out forwards; }
        .animate-pop { animation: popOut 0.6s ease-out forwards; }
        /* 通用样式 + 中文输入兼容性优化 */
        [contenteditable] { 
            outline: none; 
            font-family: "Microsoft YaHei", "微软雅黑", sans-serif; /* 适配中文字体 */
        }
        [contenteditable]:focus { 
            background-color: #f8fafc; 
            caret-color: #1f2937; /* 显示光标，提升输入体验 */
        }
        .score-block { border-radius: 8px; display: flex; align-items: center; justify-content: center; }
        .team-main-block { display: flex; align-items: center; justify-content: space-between; gap: 4px; font-weight: bold; color: white; text-shadow: 0 0 2px #000, 0 0 5px #000; }
        .team-center { justify-content: center !important; }
        /* OBS核心优化：高清抗锯齿+透明背景 */
        * { -webkit-font-smoothing: antialiased; -moz-osx-font-smoothing: grayscale; }
        #summaryPreview { background: transparent !important; box-shadow: none !important; }
        /* 比分+标题隐藏状态类 */
        .score-hidden { opacity: 0; pointer-events: none; display: none !important; }
        .score-visible { opacity: 1; pointer-events: all; display: flex !important; }
    </style>
</head>
<body class="bg-gray-100 min-h-screen p-3 md:p-4 flex flex-col items-center m-0">
    <!-- 功能操作区（交互时可见，OBS端移出画布） -->
    <div id="scoreboard" class="w-full max-w-5xl bg-white rounded-2xl shadow-xl p-4 md:p-5 mb-4 border border-gray-100">
        <div class="text-center mb-4">
            <!-- 新增 lang="zh-CN"，优化中文输入 -->
            <div id="matchName" contenteditable="true" lang="zh-CN" class="text-xl md:text-2xl font-bold text-gray-800 py-1 px-3 w-full text-center bg-gray-50 rounded-lg">2025篮球联赛-总决赛</div>
        </div>
        <div class="flex flex-col md:flex-row items-center justify-center gap-4 md:gap-5 mb-4 w-full">
            <!-- 主队模块 -->
            <div class="flex flex-col items-center gap-2 p-3 rounded-xl bg-gray-50 w-full md:w-[45%]">
                <!-- 新增 lang="zh-CN" -->
                <div class="team-name text-lg md:text-xl font-bold py-1 px-4 w-full text-center" id="teamAName" contenteditable="true" lang="zh-CN">主队</div>
                <input type="color" id="teamAColor" class="w-9 h-9 rounded-full cursor-pointer shadow-sm" value="#E53E3E">
                <div class="text-4xl md:text-5xl font-bold my-1 text-gray-800" id="teamAMainScore">0</div>
                <div class="team-subscore my-0 text-xl text-gray-600" id="teamASubScore">0</div>
                <div class="flex gap-2 mt-1">
                    <button class="btn-main-minus bg-red-500 hover:bg-red-600 text-white px-4 py-1.5 rounded-lg font-bold text-sm transition-all" data-team="A" data-type="main" data-op="-">-</button>
                    <button class="btn-main-plus bg-green-500 hover:bg-green-600 text-white px-4 py-1.5 rounded-lg font-bold text-sm transition-all" data-team="A" data-type="main" data-op="+">+</button>
                </div>
                <div class="flex gap-2 mt-1">
                    <button class="btn-sub-minus bg-red-400 hover:bg-red-500 text-white px-3 py-1 rounded-md text-xs font-medium transition-all" data-team="A" data-type="sub" data-op="-">小分-</button>
                    <button class="btn-sub-plus bg-green-400 hover:bg-green-500 text-white px-3 py-1 rounded-md text-xs font-medium transition-all" data-team="A" data-type="sub" data-op="+">小分+</button>
                </div>
            </div>
            <div class="text-center text-xl md:text-2xl font-bold text-gray-300 w-8">VS</div>
            <!-- 客队模块 -->
            <div class="flex flex-col items-center gap-2 p-3 rounded-xl bg-gray-50 w-full md:w-[45%]">
                <!-- 新增 lang="zh-CN" -->
                <div class="team-name text-lg md:text-xl font-bold py-1 px-4 w-full text-center" id="teamBName" contenteditable="true" lang="zh-CN">客队</div>
                <input type="color" id="teamBColor" class="w-9 h-9 rounded-full cursor-pointer shadow-sm" value="#2563EB">
                <div class="text-4xl md:text-5xl font-bold my-1 text-gray-800" id="teamBMainScore">0</div>
                <div class="team-subscore my-0 text-xl text-gray-600" id="teamBSubScore">0</div>
                <div class="flex gap-2 mt-1">
                    <button class="btn-main-minus bg-red-500 hover:bg-red-600 text-white px-4 py-1.5 rounded-lg font-bold text-sm transition-all" data-team="B" data-type="main" data-op="-">-</button>
                    <button class="btn-main-plus bg-green-500 hover:bg-green-600 text-white px-4 py-1.5 rounded-lg font-bold text-sm transition-all" data-team="B" data-type="main" data-op="+">+</button>
                </div>
                <div class="flex gap-2 mt-1">
                    <button class="btn-sub-minus bg-red-400 hover:bg-red-500 text-white px-3 py-1 rounded-md text-xs font-medium transition-all" data-team="B" data-type="sub" data-op="-">小分-</button>
                    <button class="btn-sub-plus bg-green-400 hover:bg-green-500 text-white px-3 py-1 rounded-md text-xs font-medium transition-all" data-team="B" data-type="sub" data-op="+">小分+</button>
                </div>
            </div>
        </div>
        <!-- 功能控制区 -->
        <div class="flex flex-wrap justify-center gap-2 py-3 rounded-xl bg-gray-50 mt-2">
            <div class="flex items-center gap-2 px-1 py-1 shrink-0">
                <input type="checkbox" id="showMainScore" checked class="w-4 h-4 cursor-pointer accent-blue-600">
                <label for="showMainScore" class="font-medium text-gray-700 text-sm">显示大比分</label>
            </div>
            <!-- 动画按钮 -->
            <button id="triggerSlideAnim" class="bg-blue-600 hover:bg-blue-700 text-white px-3 py-1.5 rounded-lg font-medium transition-all text-xs shrink-0">弹入显示比分</button>
            <button id="triggerPopAnim" class="bg-purple-600 hover:bg-purple-700 text-white px-3 py-1.5 rounded-lg font-medium transition-all text-xs shrink-0">弹出隐藏比分</button>
            <!-- 独立比分隐藏/显示按钮 -->
            <button id="toggleScoreDisplay" class="bg-cyan-600 hover:bg-cyan-700 text-white px-3 py-1.5 rounded-lg font-medium transition-all text-xs shrink-0">隐藏比分</button>
            <button id="togglePreviewBg" class="bg-orange-600 hover:bg-orange-700 text-white px-3 py-1.5 rounded-lg font-medium transition-all text-xs shrink-0">切换背景</button>
            <button id="resetAllScores" class="bg-cyan-600 hover:bg-cyan-700 text-white px-3 py-1.5 rounded-lg font-medium transition-all text-xs shrink-0">比分清零</button>
            <!-- 配色预设 -->
            <div class="flex gap-1 mt-1 md:mt-0 shrink-0 flex-wrap">
                <button class="color-preset" data-a="#E53E3E" data-b="#2563EB" title="经典红蓝" style="background:linear-gradient(90deg,#E53E3E 50%,#2563EB 50%);width:20px;height:20px;border-radius:4px;cursor:pointer;"></button>
                <button class="color-preset" data-a="#92400E" data-b="#7C2D12" title="紫金豪门" style="background:linear-gradient(90deg,#92400E 50%,#7C2D12 50%);width:20px;height:20px;border-radius:4px;cursor:pointer;"></button>
                <button class="color-preset" data-a="#166534" data-b="#F59E0B" title="绿金凯尔特人" style="background:linear-gradient(90deg,#166534 50%,#F59E0B 50%);width:20px;height:20px;border-radius:4px;cursor:pointer;"></button>
                <button class="color-preset" data-a="#0F172A" data-b="#FBBF24" title="黑黄湖人" style="background:linear-gradient(90deg,#0F172A 50%,#FBBF24 50%);width:20px;height:20px;border-radius:4px;cursor:pointer;"></button>
                <button class="color-preset" data-a="#F97316" data-b="#0369A1" title="橙蓝勇士" style="background:linear-gradient(90deg,#F97316 50%,#0369A1 50%);width:20px;height:20px;border-radius:4px;cursor:pointer;"></button>
                <button class="color-preset" data-a="#A855F7" data-b="#EC4899" title="粉紫潮流" style="background:linear-gradient(90deg,#A855F7 50%,#EC4899 50%);width:20px;height:20px;border-radius:4px;cursor:pointer;"></button>
                <button class="color-preset" data-a="#0D9488" data-b="#64748B" title="青灰简约" style="background:linear-gradient(90deg,#0D9488 50%,#64748B 50%);width:20px;height:20px;border-radius:4px;cursor:pointer;"></button>
            </div>
        </div>
    </div>

    <!-- OBS展示区（透明比分区） -->
    <div id="summaryPreview" class="w-full max-w-6xl bg-transparent rounded-2xl p-4 md:p-5 flex flex-col gap-3 items-center justify-center">
        <!-- 比分+标题总容器（统一控制显隐） -->
        <div id="scoreTitleCore" class="w-full flex flex-col gap-3 items-center justify-center score-visible">
            <div id="summaryMatchName" class="text-white text-lg md:text-xl font-bold text-center w-full drop-shadow-md">2025篮球联赛-总决赛</div>
            <!-- 比分核心区 -->
            <div id="scoreCore" class="flex items-center justify-center gap-3 w-full flex-wrap">
                <div id="summaryATeam" class="team-main-block score-block px-4 py-2 min-w-[140px] text-xl md:text-2xl">
                    <span id="summaryAMain">0</span>
                    <span id="summaryAName">主队</span>
                </div>
                <div id="summaryASub" class="score-block px-5 py-2 bg-white text-gray-900 text-2xl md:text-3xl font-bold text-center min-w-[70px]">0</div>
                <div class="text-white text-2xl md:text-3xl font-bold px-2 drop-shadow-md">VS</div>
                <div id="summaryBSub" class="score-block px-5 py-2 bg-white text-gray-900 text-2xl md:text-3xl font-bold text-center min-w-[70px]">0</div>
                <div id="summaryBTeam" class="team-main-block score-block px-4 py-2 min-w-[140px] text-xl md:text-2xl">
                    <span id="summaryBName">客队</span>
                    <span id="summaryBMain">0</span>
                </div>
            </div>
        </div>
    </div>

    <script>
        // 全局变量初始化 + 新增定时器存储变量（用于清除）
        const summaryPreview = document.getElementById('summaryPreview');
        const scoreTitleCore = document.getElementById('scoreTitleCore'); // 标题+比分总容器
        const summaryMatchName = document.getElementById('summaryMatchName');
        const scoreCore = document.getElementById('scoreCore');
        const summaryATeam = document.getElementById('summaryATeam');
        const summaryAName = document.getElementById('summaryAName');
        const summaryAMain = document.getElementById('summaryAMain');
        const summaryASub = document.getElementById('summaryASub');
        const summaryBTeam = document.getElementById('summaryBTeam');
        const summaryBName = document.getElementById('summaryBName');
        const summaryBMain = document.getElementById('summaryBMain');
        const summaryBSub = document.getElementById('summaryBSub');
        // 功能按钮
        const togglePreviewBg = document.getElementById('togglePreviewBg');
        const triggerSlideAnim = document.getElementById('triggerSlideAnim');
        const triggerPopAnim = document.getElementById('triggerPopAnim');
        const toggleScoreDisplay = document.getElementById('toggleScoreDisplay');
        const resetAllScores = document.getElementById('resetAllScores');
        // 背景色数组
        const bgColors = ['transparent', '#000000', '#111827', '#1E293B', '#0F172A'];
        let bgIndex = 0;
        // 比分显示状态（默认显示）
        let isScoreVisible = true;
        // ✅ 新增：存储隐藏定时器，用于显示时清除
        let hideTimer=null;

        const teamA = { name: document.getElementById('teamAName'), color: document.getElementById('teamAColor'), mainScore: document.getElementById('teamAMainScore'), subScore: document.getElementById('teamASubScore') };
        const teamB = { name: document.getElementById('teamBName'), color: document.getElementById('teamBColor'), mainScore: document.getElementById('teamBMainScore'), subScore: document.getElementById('teamBSubScore') };
        const matchName = document.getElementById('matchName');
        const showMainScore = document.getElementById('showMainScore');

        // 1. 队名+颜色同步逻辑
        function bindTeamStyle(team, summaryTeamBlock) {
            team.color.addEventListener('input', () => {
                team.name.style.color = team.color.value;
                summaryTeamBlock.style.backgroundColor = team.color.value;
            });
            team.name.addEventListener('input', updateSummary);
            team.name.style.color = team.color.value;
            summaryTeamBlock.style.backgroundColor = team.color.value;
        }
        bindTeamStyle(teamA, summaryATeam);
        bindTeamStyle(teamB, summaryBTeam);
        matchName.addEventListener('input', updateSummary);

        // 2. 比分加减控制（防负数）
        document.querySelectorAll('[data-team][data-type][data-op]').forEach(btn => {
            btn.addEventListener('click', () => {
                const { team, type, op } = btn.dataset;
                const targetTeam = team === 'A' ? teamA : teamB;
                const scoreEl = type === 'main' ? targetTeam.mainScore : targetTeam.subScore;
                let currentScore = parseInt(scoreEl.textContent) || 0;
                currentScore = op === '+' ? currentScore + 1 : Math.max(0, currentScore - 1);
                scoreEl.textContent = currentScore;
                updateSummary();
            });
        });

        // 3. 大比分显隐控制
        showMainScore.addEventListener('change', () => {
            const isShow = showMainScore.checked;
            summaryAMain.style.display = isShow ? 'inline-block' : 'none';
            summaryBMain.style.display = isShow ? 'inline-block' : 'none';
            isShow 
                ? (summaryATeam.classList.remove('team-center'), summaryBTeam.classList.remove('team-center'))
                : (summaryATeam.classList.add('team-center'), summaryBTeam.classList.add('team-center'));
        });

        // 4. 背景切换功能（完整生效）
        togglePreviewBg.addEventListener('click', () => {
            bgIndex = (bgIndex + 1) % bgColors.length;
            summaryPreview.style.backgroundColor = bgColors[bgIndex];
            summaryPreview.style.background = bgColors[bgIndex] === 'transparent' ? 'transparent' : bgColors[bgIndex];
        });

        // ✅ 5. 动画逻辑修复：清除定时器+严格状态同步
        function playAnimation(animClass, isShow) {
            // 清除所有残留的隐藏定时器（根治显示后消失的BUG）
            if (hideTimer) {
                clearTimeout(hideTimer);
                hideTimer=null;
            }
            // 移除所有动画类，强制重绘
            scoreTitleCore.classList.remove('animate-slide', 'animate-pop');
            void scoreTitleCore.offsetWidth;

            if (isShow) {
                // 弹入显示：标题+比分同时显示并播放动画
                scoreTitleCore.classList.remove('score-hidden');
                scoreTitleCore.classList.add('score-visible', animClass);
                isScoreVisible=true;
                toggleScoreDisplay.textContent = '隐藏比分';
            } else {
                // 弹出隐藏：播放动画后隐藏标题+比分，存储定时器以便后续清除
                scoreTitleCore.classList.add(animClass);
                hideTimer=setTimeout(() => {
                    scoreTitleCore.classList.remove('score-visible');
                    scoreTitleCore.classList.add('score-hidden');
                    isScoreVisible=false;
                    toggleScoreDisplay.textContent = '显示比分';
                    hideTimer=null; // 定时器执行后清空
                }, 600); // 与动画时长一致
            }
        }

        // 弹入显示比分（标题+比分同步）
        triggerSlideAnim.addEventListener('click', () => {
            playAnimation('animate-slide', true);
        });

        // 弹出隐藏比分（标题+比分同步）
        triggerPopAnim.addEventListener('click', () => {
            playAnimation('animate-pop', false);
        });

        // ✅ 6. 独立比分隐藏/显示按钮修复：清除定时器+状态同步
        toggleScoreDisplay.addEventListener('click', () => {
            // 清除隐藏定时器，防止显示后立即隐藏
            if (hideTimer) {
                clearTimeout(hideTimer);
                hideTimer=null;
            }
            if (isScoreVisible) {
                // 隐藏：标题+比分立即消失
                scoreTitleCore.classList.remove('score-visible');
                scoreTitleCore.classList.add('score-hidden');
                isScoreVisible=false;
                toggleScoreDisplay.textContent = '显示比分';
            } else {
                // 显示：标题+比分立即出现（无动画）
                scoreTitleCore.classList.remove('score-hidden');
                scoreTitleCore.classList.add('score-visible');
                isScoreVisible=true;
                toggleScoreDisplay.textContent = '隐藏比分';
            }
        });

        // 7. 一键比分清零功能
        resetAllScores.addEventListener('click', () => {
            teamA.mainScore.textContent = '0';
            teamA.subScore.textContent = '0';
            teamB.mainScore.textContent = '0';
            teamB.subScore.textContent = '0';
            updateSummary();
        });

        // 8. 配色预设功能
        document.querySelectorAll('.color-preset').forEach(btn => {
            btn.addEventListener('click', () => {
                const colorA = btn.dataset.a, colorB = btn.dataset.b;
                teamA.color.value = colorA;
                teamA.name.style.color = colorA;
                teamB.color.value = colorB;
                teamB.name.style.color = colorB;
                summaryATeam.style.backgroundColor = colorA;
                summaryBTeam.style.backgroundColor = colorB;
            });
        });

        // 9. 数据实时同步预览区
        function updateSummary() {
            summaryMatchName.textContent = matchName.textContent.trim() || '篮球赛事';
            summaryAName.textContent = teamA.name.textContent.trim() || '主队';
            summaryAMain.textContent = teamA.mainScore.textContent;
            summaryASub.textContent = teamA.subScore.textContent;
            summaryBName.textContent = teamB.name.textContent.trim() || '客队';
            summaryBMain.textContent = teamB.mainScore.textContent;
            summaryBSub.textContent = teamB.subScore.textContent;
        }

        // 初始化：显示比分+同步数据
        updateSummary();
        if (!showMainScore.checked) {
            summaryATeam.classList.add('team-center');
            summaryBTeam.classList.add('team-center');
        }
    </script>
</body>
</html>
