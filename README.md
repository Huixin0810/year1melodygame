<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>旋律小侦探 🎵 - 音乐听力大冒险</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Canvas Confetti for Victory -->
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Fredoka:wght@400;600;700&family=ZCOOL+KuaiLe&display=swap');

        body {
            font-family: 'Fredoka', 'ZCOOL KuaiLe', cursive, sans-serif;
            background: linear-gradient(135deg, #ff9a9e 0%, #fecfef 50%, #a1c4fd 100%);
            min-height: 100vh;
            user-select: none;
        }

        .bouncy-btn {
            transition: transform 0.15s cubic-bezier(0.175, 0.885, 0.32, 1.275), box-shadow 0.15s ease;
        }
        .bouncy-btn:hover {
            transform: translateY(-4px) scale(1.03);
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.15);
        }
        .bouncy-btn:active {
            transform: translateY(2px) scale(0.97);
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        }

        /* Note bubble animation */
        .note-bubble {
            transition: all 0.3s ease;
        }
        .note-bubble.playing {
            transform: scale(1.35);
            filter: drop-shadow(0 0 12px #ff4081);
        }

        /* Custom staff line styling */
        .staff-line {
            height: 3px;
            background-color: #cbd5e1;
            border-radius: 2px;
        }

        /* Glassmorphism panel */
        .glass-card {
            background: rgba(255, 255, 255, 0.88);
            backdrop-filter: blur(12px);
            border: 4px solid rgba(255, 255, 255, 0.9);
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
        }

        .pulse-light {
            animation: pulse-glow 2s infinite;
        }
        @keyframes pulse-glow {
            0%, 100% { transform: scale(1); opacity: 0.9; }
            50% { transform: scale(1.05); opacity: 1; }
        }

        /* Floating music notes background */
        .floating-bg-note {
            position: absolute;
            opacity: 0.15;
            animation: float 8s infinite ease-in-out;
            z-index: 0;
        }
        @keyframes float {
            0%, 100% { transform: translateY(0) rotate(0deg); }
            50% { transform: translateY(-25px) rotate(15deg); }
        }
    </style>
</head>
<body class="flex items-center justify-center p-4 relative overflow-x-hidden">

    <!-- Background decorative floating elements -->
    <i class="fa-solid fa-music floating-bg-note text-6xl text-pink-500 top-10 left-10" style="animation-delay: 0s;"></i>
    <i class="fa-solid fa-icons floating-bg-note text-7xl text-yellow-400 top-1/4 right-12" style="animation-delay: 2s;"></i>
    <i class="fa-solid fa-note-sticky floating-bg-note text-6xl text-blue-400 bottom-12 left-16" style="animation-delay: 4s;"></i>
    <i class="fa-solid fa-guitar floating-bg-note text-8xl text-purple-400 bottom-20 right-20" style="animation-delay: 1s;"></i>

    <!-- Main Container -->
    <div class="w-full max-w-2xl relative z-10">

        <!-- Welcome / Start Screen -->
        <div id="start-screen" class="glass-card rounded-3xl p-8 text-center my-6 transform transition-all">
            <div class="inline-block bg-yellow-300 text-yellow-900 px-6 py-2 rounded-full font-bold text-sm tracking-wider uppercase mb-4 shadow-sm animate-bounce">
                ✨ 趣味音乐听觉小游戏
            </div>
            <h1 class="text-4xl md:text-5xl font-extrabold text-pink-600 mb-4 tracking-wide">
                旋律小侦探 🎵
            </h1>
            <p class="text-gray-600 text-lg mb-6 leading-relaxed">
                戴上耳机或打开声音，倾听耳朵听到的音符变化！<br>
                辨别它们是 <span class="text-pink-500 font-bold">📈 上行</span>、<span class="text-blue-500 font-bold">📉 下行</span> 还是 <span class="text-green-500 font-bold">➡️ 平行</span> 旋律。
            </p>

            <!-- Knowledge Prep Cards -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-3 text-left mb-8">
                <div class="bg-pink-100/80 p-3 rounded-2xl border-2 border-pink-200">
                    <div class="text-pink-600 font-bold mb-1 flex items-center gap-1">
                        <i class="fa-solid fa-arrow-trend-up"></i> 上行旋律
                    </div>
                    <div class="text-xs text-gray-600">声音从低音到高音，像小火箭踩着阶梯往上爬。</div>
                </div>
                <div class="bg-blue-100/80 p-3 rounded-2xl border-2 border-blue-200">
                    <div class="text-blue-600 font-bold mb-1 flex items-center gap-1">
                        <i class="fa-solid fa-arrow-trend-down"></i> 下行旋律
                    </div>
                    <div class="text-xs text-gray-600">声音从高音到低音，像滑滑梯或雨滴落下。</div>
                </div>
                <div class="bg-green-100/80 p-3 rounded-2xl border-2 border-green-200">
                    <div class="text-green-600 font-bold mb-1 flex items-center gap-1">
                        <i class="fa-solid fa-grip-lines"></i> 平行旋律
                    </div>
                    <div class="text-xs text-gray-600">音高保持平稳不变，像在平地上走或同音重复。</div>
                </div>
            </div>

            <button id="start-btn" onclick="startGame()" class="bouncy-btn bg-gradient-to-r from-pink-500 to-rose-400 hover:from-pink-600 hover:to-rose-500 text-white font-extrabold text-2xl py-4 px-10 rounded-full shadow-lg border-4 border-white transition-all flex items-center justify-center gap-3 mx-auto">
                <i class="fa-solid fa-play"></i> 开始大冒险 (一共8题)
            </button>
        </div>

        <!-- Quiz Screen -->
        <div id="quiz-screen" class="glass-card rounded-3xl p-6 md:p-8 hidden relative transition-all">

            <!-- Top Header: Level & Score -->
            <div class="flex justify-between items-center mb-6">
                <div class="flex items-center gap-2 bg-pink-100 px-4 py-2 rounded-full border border-pink-200">
                    <i class="fa-solid fa-flag text-pink-500"></i>
                    <span id="question-number" class="font-bold text-pink-700 text-lg">关卡 1 / 8</span>
                </div>
                <div class="flex items-center gap-2 bg-yellow-100 px-4 py-2 rounded-full border border-yellow-200">
                    <i class="fa-solid fa-star text-yellow-500"></i>
                    <span id="score-display" class="font-bold text-yellow-700 text-lg">得分: 0</span>
                </div>
            </div>

            <!-- Progress Bar -->
            <div class="w-full bg-gray-200 h-3 rounded-full overflow-hidden mb-6">
                <div id="progress-bar" class="bg-gradient-to-r from-pink-400 to-purple-500 h-full w-0 transition-all duration-500"></div>
            </div>

            <!-- Question Card Header -->
            <div class="text-center mb-6">
                <h2 id="question-title" class="text-2xl md:text-3xl font-extrabold text-gray-800 mb-2">
                    关卡加载中...
                </h2>
                <p id="question-desc" class="text-gray-500 text-sm">点击下方播放按钮，仔细听旋律的走向吧！</p>
            </div>

            <!-- Music Play & Visualizer Staff -->
            <div class="bg-white/90 rounded-2xl p-6 mb-6 shadow-inner border-2 border-purple-100 relative overflow-hidden text-center">

                <!-- Staff Lines Background -->
                <div class="relative h-32 w-full max-w-md mx-auto flex flex-col justify-between py-2 mb-4">
                    <div class="staff-line"></div>
                    <div class="staff-line"></div>
                    <div class="staff-line"></div>
                    <div class="staff-line"></div>
                    <div class="staff-line"></div>

                    <!-- Animated Musical Note Heads -->
                    <div id="notes-container" class="absolute inset-0 flex justify-around items-center px-6">
                        <!-- Dynamic Notes render here -->
                    </div>
                </div>

                <!-- Play Audio Button -->
                <button id="play-btn" onclick="playCurrentMelody()" class="bouncy-btn bg-yellow-400 hover:bg-yellow-300 text-yellow-950 font-extrabold text-xl py-3 px-8 rounded-full shadow-md border-2 border-yellow-200 inline-flex items-center gap-3 cursor-pointer">
                    <i class="fa-solid fa-volume-high text-2xl animate-pulse"></i>
                    <span id="play-btn-text">播放旋律</span>
                </button>
            </div>

            <!-- Option Buttons Grid -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
                <button onclick="checkAnswer('ascending')" class="bouncy-btn bg-pink-500 hover:bg-pink-600 text-white p-4 rounded-2xl font-bold text-lg shadow-md border-b-4 border-pink-700 flex flex-col items-center justify-center gap-1">
                    <span class="text-2xl">📈</span>
                    <span>上行旋律</span>
                </button>
                <button onclick="checkAnswer('descending')" class="bouncy-btn bg-sky-500 hover:bg-sky-600 text-white p-4 rounded-2xl font-bold text-lg shadow-md border-b-4 border-sky-700 flex flex-col items-center justify-center gap-1">
                    <span class="text-2xl">📉</span>
                    <span>下行旋律</span>
                </button>
                <button onclick="checkAnswer('parallel')" class="bouncy-btn bg-emerald-500 hover:bg-emerald-600 text-white p-4 rounded-2xl font-bold text-lg shadow-md border-b-4 border-emerald-700 flex flex-col items-center justify-center gap-1">
                    <span class="text-2xl">➡️</span>
                    <span>平行旋律</span>
                </button>
            </div>

            <!-- Feedback Alert / Explanation Box -->
            <div id="feedback-box" class="hidden p-4 rounded-2xl mb-4 text-center transform transition-all border-2">
                <div id="feedback-title" class="font-extrabold text-xl mb-1"></div>
                <div id="feedback-reason" class="text-sm"></div>
                <button id="next-btn" onclick="nextQuestion()" class="mt-3 bg-gray-800 text-white hover:bg-gray-900 font-bold py-2 px-6 rounded-full text-sm shadow bouncy-btn">
                    下一题 <i class="fa-solid fa-arrow-right"></i>
                </button>
            </div>
        </div>

        <!-- Victory / Result Screen -->
        <div id="result-screen" class="glass-card rounded-3xl p-8 text-center hidden transform transition-all">
            <div class="text-6xl mb-4">🏆</div>
            <h1 class="text-4xl font-extrabold text-purple-700 mb-2">冒险通关啦！</h1>
            <p class="text-gray-600 text-lg mb-6">你已经是一位出色的旋律小侦探！</p>

            <div class="bg-white/80 rounded-2xl p-6 max-w-sm mx-auto shadow-md border border-purple-100 mb-8">
                <div class="text-gray-500 text-sm mb-1">最终得分</div>
                <div id="final-score" class="text-5xl font-black text-yellow-500 mb-2">100</div>
                <div id="star-rating" class="text-3xl text-yellow-400 tracking-wider">⭐⭐⭐</div>
                <p id="eval-text" class="text-gray-600 mt-3 font-bold">完美的听觉判断力！</p>
            </div>

            <button onclick="restartGame()" class="bouncy-btn bg-gradient-to-r from-purple-500 to-pink-500 hover:from-purple-600 hover:to-pink-600 text-white font-extrabold text-xl py-4 px-10 rounded-full shadow-lg border-4 border-white inline-flex items-center gap-3">
                <i class="fa-solid fa-rotate-right"></i> 再玩一次
            </button>
        </div>

    </div>

    <!-- Web Audio & Game Logic -->
    <script>
        // Note standard frequency chart (Hz)
        const NOTE_FREQS = {
            'C4': 261.63, 'D4': 293.66, 'E4': 329.63, 'F4': 349.23, 'G4': 392.00, 'A4': 440.00, 'B4': 493.88,
            'C5': 523.25, 'D5': 587.33, 'E5': 659.25, 'F5': 698.46, 'G5': 783.99, 'A5': 880.00, 'B5': 987.77,
            'C6': 1046.50
        };

        // Pitch height mapping for visual position on staff (percentage from bottom)
        const NOTE_HEIGHTS = {
            'C4': 10, 'D4': 20, 'E4': 30, 'F4': 40, 'G4': 50,
            'A4': 60, 'B4': 70, 'C5': 80, 'D5': 88, 'E5': 95
        };

        // Note colors for cute aesthetic
        const NOTE_COLORS = [
            'bg-pink-400', 'bg-yellow-400', 'bg-emerald-400', 'bg-sky-400', 'bg-purple-400', 'bg-orange-400'
        ];

        // 8 Carefully Designed Levels
        const QUIZ_QUESTIONS = [
            {
                id: 1,
                title: "1. 🚀 小火箭升空",
                desc: "听一听这四个音符，声音是在怎么变化？",
                notes: ['C4', 'E4', 'G4', 'C5'],
                type: 'ascending',
                explanation: "音符从低音 C4 一路升到了高音 C5，就像小火箭飞向太空，属于典型的【上行旋律】！"
            },
            {
                id: 2,
                title: "2. 🌧️ 云朵下雨啦",
                desc: "仔细听听看，声音是越来越高还是越来越低？",
                notes: ['C5', 'A4', 'F4', 'D4'],
                type: 'descending',
                explanation: "音符从高音 C5 逐步降低到 D4，就像雨滴从云层飘落，属于【下行旋律】！"
            },
            {
                id: 3,
                title: "3. 🦘 小袋鼠原地跳",
                desc: "这个旋律的声音有什么特别之处？",
                notes: ['G4', 'G4', 'G4', 'G4'],
                type: 'parallel',
                explanation: "所有音符的音高完全相同（都是 Sol/G4），高度保持平行持平，属于【平行旋律】！"
            },
            {
                id: 4,
                title: "4. ⛰️ 爬山小达人",
                desc: "听听这段优美的声音走向吧！",
                notes: ['D4', 'F4', 'A4', 'D5'],
                type: 'ascending',
                explanation: "每个音符都比前一个音更高，声音一阶阶往上升，属于【上行旋律】！"
            },
            {
                id: 5,
                title: "5. 🐥 小黄鸭平平游",
                desc: "小黄鸭在平静的水面上滑行，声音是怎样呢？",
                notes: ['E4', 'E4', 'E4', 'E4'],
                type: 'parallel',
                explanation: "连续四个相同音高的音符（Mi/E4），音高没有上下起伏，属于【平行旋律】！"
            },
            {
                id: 6,
                title: "6. 🛝 快乐溜滑梯",
                desc: "声音是从高处往低处走，还是低处往高处走？",
                notes: ['B4', 'G4', 'E4', 'C4'],
                type: 'descending',
                explanation: "音高一路下滑，从高音 B4 降到低音 C4，像顺着滑梯溜下来，属于【下行旋律】！"
            },
            {
                id: 7,
                title: "7. 🌈 踩着彩虹阶梯",
                desc: "听听这段连续音符的走向。",
                notes: ['C4', 'D4', 'E4', 'F4', 'G4'],
                type: 'ascending',
                explanation: "Do-Re-Mi-Fa-Sol 连续递增上升，呈现完美的斜向上线条，属于【上行旋律】！"
            },
            {
                id: 8,
                title: "8. 📻 摩尔斯电码滴滴声",
                desc: "最后一关！听听这段节奏感十足的声音！",
                notes: ['A4', 'A4', 'A4', 'A4'],
                type: 'parallel',
                explanation: "声音的高低保持在同一条水平线上，没有升高也没有降低，属于【平行旋律】！"
            }
        ];

        let currentQuestionIndex = 0;
        let score = 0;
        let isPlayingAudio = false;
        let audioCtx = null;
        let hasAnsweredCurrent = false;

        // Initialize Audio Context on user click (browser policy requirement)
        function initAudio() {
            if (!audioCtx) {
                const AudioContext = window.AudioContext || window.webkitAudioContext;
                audioCtx = new AudioContext();
            }
            if (audioCtx.state === 'suspended') {
                audioCtx.resume();
            }
        }

        // Play a single synthesized tone with pleasant warm piano/bell envelope
        function playTone(freq, duration = 0.45, delay = 0) {
            return new Promise((resolve) => {
                setTimeout(() => {
                    if (!audioCtx) return resolve();

                    const osc = audioCtx.createOscillator();
                    const gain = audioCtx.createGain();

                    // Triangle wave gives a soft, sweet bell-like tone
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(freq, audioCtx.currentTime);

                    // Envelope for soft attack & gentle release
                    gain.gain.setValueAtTime(0.001, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.35, audioCtx.currentTime + 0.05);
                    gain.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + duration);

                    osc.connect(gain);
                    gain.connect(audioCtx.destination);

                    osc.start(audioCtx.currentTime);
                    osc.stop(audioCtx.currentTime + duration);

                    setTimeout(resolve, duration * 1000);
                }, delay * 1000);
            });
        }

        // Play feedback sounds
        function playSoundEffect(isCorrect) {
            initAudio();
            if (isCorrect) {
                // Happy chord chime
                playTone(NOTE_FREQS['C5'], 0.15, 0);
                playTone(NOTE_FREQS['E5'], 0.15, 0.1);
                playTone(NOTE_FREQS['G5'], 0.2, 0.2);
                playTone(NOTE_FREQS['C6'], 0.4, 0.3);
            } else {
                // Gentle try-again tone
                playTone(NOTE_FREQS['G3'] || 196, 0.2, 0);
                playTone(NOTE_FREQS['E3'] || 164, 0.3, 0.18);
            }
        }

        // Game Start
        function startGame() {
            initAudio();
            currentQuestionIndex = 0;
            score = 0;
            
            document.getElementById('start-screen').classList.add('hidden');
            document.getElementById('result-screen').classList.add('hidden');
            document.getElementById('quiz-screen').classList.remove('hidden');

            loadQuestion();
        }

        // Load Question & Render Visual Notes
        function loadQuestion() {
            hasAnsweredCurrent = false;
            const q = QUIZ_QUESTIONS[currentQuestionIndex];

            document.getElementById('question-number').innerText = `关卡 ${currentQuestionIndex + 1} / ${QUIZ_QUESTIONS.length}`;
            document.getElementById('score-display').innerText = `得分: ${score}`;
            document.getElementById('question-title').innerText = q.title;
            document.getElementById('question-desc').innerText = q.desc;

            // Reset Feedback box
            const feedbackBox = document.getElementById('feedback-box');
            feedbackBox.classList.add('hidden');

            // Update Progress Bar
            const progressPercent = ((currentQuestionIndex) / QUIZ_QUESTIONS.length) * 100;
            document.getElementById('progress-bar').style.width = `${progressPercent}%`;

            // Render Animated Staff Notes
            const notesContainer = document.getElementById('notes-container');
            notesContainer.innerHTML = '';

            q.notes.forEach((noteName, idx) => {
                const heightPercent = NOTE_HEIGHTS[noteName] || 50;
                const colorClass = NOTE_COLORS[idx % NOTE_COLORS.length];

                const noteEl = document.createElement('div');
                noteEl.id = `note-bubble-${idx}`;
                noteEl.className = `note-bubble relative flex items-center justify-center w-10 h-10 md:w-12 md:h-12 rounded-full ${colorClass} text-white font-black text-sm shadow-md border-2 border-white`;
                noteEl.style.bottom = `${heightPercent - 30}%`;
                noteEl.innerHTML = `<i class="fa-solid fa-music"></i>`;

                notesContainer.appendChild(noteEl);
            });

            // Automatically play melody on load with short delay
            setTimeout(() => {
                playCurrentMelody();
            }, 400);
        }

        // Play audio sequence of current question & animate note bubbles
        async function playCurrentMelody() {
            if (isPlayingAudio) return;
            initAudio();
            isPlayingAudio = true;

            const playBtnText = document.getElementById('play-btn-text');
            const playBtn = document.getElementById('play-btn');
            playBtnText.innerText = "播放中...";
            playBtn.classList.add('opacity-75');

            const currentNotes = QUIZ_QUESTIONS[currentQuestionIndex].notes;

            for (let i = 0; i < currentNotes.length; i++) {
                const noteName = currentNotes[i];
                const freq = NOTE_FREQS[noteName];
                const noteEl = document.getElementById(`note-bubble-${i}`);

                if (noteEl) noteEl.classList.add('playing');
                
                await playTone(freq, 0.45);

                if (noteEl) noteEl.classList.remove('playing');
            }

            isPlayingAudio = false;
            playBtnText.innerText = "再听一次";
            playBtn.classList.remove('opacity-75');
        }

        // Verify User Answer
        function checkAnswer(selectedType) {
            if (hasAnsweredCurrent) return;
            hasAnsweredCurrent = true;

            const currentQ = QUIZ_QUESTIONS[currentQuestionIndex];
            const isCorrect = selectedType === currentQ.type;

            const feedbackBox = document.getElementById('feedback-box');
            const feedbackTitle = document.getElementById('feedback-title');
            const feedbackReason = document.getElementById('feedback-reason');

            playSoundEffect(isCorrect);

            if (isCorrect) {
                score += 12.5; // 8 questions * 12.5 = 100
                feedbackBox.className = "p-4 rounded-2xl mb-4 text-center transform transition-all border-2 bg-green-100 border-green-300 text-green-900";
                feedbackTitle.innerHTML = "🎉 太棒啦！回答正确！";
            } else {
                feedbackBox.className = "p-4 rounded-2xl mb-4 text-center transform transition-all border-2 bg-orange-100 border-orange-300 text-orange-900";
                feedbackTitle.innerHTML = "💡 加油，差一点点哦！";
            }

            feedbackReason.innerText = currentQ.explanation;
            feedbackBox.classList.remove('hidden');

            document.getElementById('score-display').innerText = `得分: ${Math.round(score)}`;
        }

        // Next Question
        function nextQuestion() {
            currentQuestionIndex++;
            if (currentQuestionIndex < QUIZ_QUESTIONS.length) {
                loadQuestion();
            } else {
                showFinalResults();
            }
        }

        // Show Final Victory Screen
        function showFinalResults() {
            document.getElementById('quiz-screen').classList.add('hidden');
            document.getElementById('result-screen').classList.remove('hidden');

            const finalScore = Math.round(score);
            document.getElementById('final-score').innerText = finalScore;

            const starRating = document.getElementById('star-rating');
            const evalText = document.getElementById('eval-text');

            if (finalScore >= 90) {
                starRating.innerText = "⭐⭐⭐";
                evalText.innerText = "哇！你是超强听觉天才！全对通关！";
                triggerConfetti();
            } else if (finalScore >= 60) {
                starRating.innerText = "⭐⭐";
                evalText.innerText = "表现很棒哦！你已经掌握了大部分旋律！";
            } else {
                starRating.innerText = "⭐";
                evalText.innerText = "继续加油！再多听几遍旋律吧！";
            }
        }

        // Confetti effect on victory
        function triggerConfetti() {
            if (typeof confetti === 'function') {
                confetti({
                    particleCount: 100,
                    spread: 70,
                    origin: { y: 0.6 }
                });
            }
        }

        // Restart Game
        function restartGame() {
            startGame();
        }
    </script>
</body>
</html>
```eof

### 🎮 游戏特色与体验亮点：

1. **真实琴音与视听联动**：通过 Web Audio API 现场实时合成清脆悦耳的琴音，无需加载外部音频文件；播放时，五线谱上的彩色音符球会随着音高高低浮动并实时放大闪烁！
2. **鲜艳马卡龙主题**：柔和鲜艳的马卡龙渐变色彩，搭配可爱圆润的字体和卡片，带给玩家轻松愉悦的学习氛围。
3. **8 道递进式关卡**：涵盖场景故事（如小火箭升空、雨滴飘落、小袋鼠跳跃等），直观理解上行旋律、下行旋律与平行旋律的特点。
4. **即时音效与动画反馈**：正确与重试均配有愉悦的悦耳提示音，通关后还支持五彩纸屑庆祝！

您可以直接在浏览器中打开此 HTML 文件进行互动体验！祝您游玩愉快！html

