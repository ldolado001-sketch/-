<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>하시교육 관리 시스템</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@400;700;900&display=swap');
        
        body {
            font-family: 'Noto Sans KR', sans-serif;
            background-color: #F1F3F5;
        }

        .glass-card {
            background: rgba(255, 255, 255, 0.7);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.5);
        }

        .scroll-hide::-webkit-scrollbar {
            display: none;
        }

        .unit-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .animate-fade-in {
            animation: fadeIn 0.6s ease-out forwards;
        }

        /* Modal Backdrop */
        .modal-overlay {
            background: rgba(0, 0, 0, 0.4);
            backdrop-filter: blur(4px);
        }
    </style>
</head>
<body class="min-h-screen flex overflow-hidden">

    <!-- Developer Login Modal -->
    <div id="login-modal" class="hidden fixed inset-0 z-50 flex items-center justify-center modal-overlay">
        <div class="bg-white rounded-[2.5rem] p-10 w-full max-w-md shadow-2xl animate-fade-in border border-slate-100">
            <h3 class="text-2xl font-black text-slate-800 mb-6 text-center">개발자 인증</h3>
            <div class="space-y-4">
                <input type="password" id="dev-password" placeholder="비밀번호를 입력하세요" 
                    class="w-full px-6 py-4 rounded-2xl bg-slate-100 border-none focus:ring-4 focus:ring-orange-100 text-lg font-bold outline-none text-center tracking-widest">
                <p id="login-error" class="text-rose-500 text-sm font-bold text-center hidden">비밀번호가 일치하지 않습니다.</p>
                <div class="flex space-x-3 mt-6">
                    <button onclick="closeLoginModal()" class="flex-1 py-4 rounded-2xl bg-slate-100 text-slate-500 font-black hover:bg-slate-200 transition-all">취소</button>
                    <button onclick="attemptLogin()" class="flex-1 py-4 rounded-2xl bg-orange-500 text-white font-black hover:bg-orange-600 transition-all shadow-lg shadow-orange-200">인증하기</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Sidebar -->
    <aside class="w-80 glass-card flex flex-col border-r border-slate-200 h-screen">
        <div class="p-10 mb-6">
            <div class="flex items-center space-x-4">
                <div class="bg-orange-500 p-3 rounded-2xl shadow-lg shadow-orange-100">
                    <i class="fas fa-star text-white text-xl"></i>
                </div>
                <div>
                    <h1 class="text-2xl font-black text-slate-800 tracking-tighter leading-none">하시교육</h1>
                    <p class="text-[10px] text-orange-600 font-black tracking-widest mt-2">4학년 교육 · 주산 전문</p>
                </div>
            </div>
        </div>
        
        <nav class="flex-1 px-6 space-y-4">
            <button onclick="router.go('dashboard')" id="nav-dashboard" class="w-full flex items-center space-x-4 px-6 py-5 rounded-[1.5rem] transition-all bg-orange-50 text-orange-600 font-black">
                <i class="fas fa-th-large text-xl"></i>
                <span class="text-lg">과목 선택</span>
            </button>
            <button onclick="router.go('download')" id="nav-download" class="w-full flex items-center space-x-4 px-6 py-5 rounded-[1.5rem] text-slate-400 font-bold hover:bg-white transition-all">
                <i class="fas fa-file-arrow-down text-xl"></i>
                <span class="text-lg">학습지 다운로드</span>
            </button>
        </nav>
        
        <div class="p-10 space-y-4">
            <button id="dev-login-btn" onclick="openLoginModal()" class="w-full flex items-center space-x-3 text-slate-400 font-bold text-base hover:text-orange-500 transition-colors">
                <i class="fas fa-code"></i>
                <span id="dev-btn-text">개발자 로그인</span>
            </button>
            <button class="w-full flex items-center space-x-3 text-slate-400 font-bold text-base hover:text-rose-500 transition-colors">
                <i class="fas fa-sign-out-alt"></i>
                <span>시스템 종료</span>
            </button>
        </div>
    </aside>

    <!-- Main Content -->
    <main class="flex-1 flex flex-col h-screen">
        <!-- Header -->
        <header class="h-24 px-12 flex items-center justify-between shrink-0">
            <div class="glass-card px-8 py-3 rounded-full shadow-sm flex items-center space-x-3">
                <span class="text-xs font-black text-slate-400 uppercase tracking-widest">Admin Center</span>
                <i class="fas fa-chevron-right text-[10px] text-slate-300"></i>
                <span class="text-xs font-black text-slate-800 uppercase tracking-widest">4th Grade Management</span>
                <div id="dev-badge" class="hidden ml-4 px-3 py-1 bg-orange-500 text-white text-[10px] font-black rounded-lg">DEV MODE</div>
            </div>
            
            <div class="flex items-center space-x-6">
                <div class="glass-card p-4 rounded-2xl cursor-pointer hover:bg-white transition-all shadow-sm">
                    <i class="fas fa-bell text-slate-600"></i>
                </div>
                <div class="w-14 h-14 rounded-2xl bg-[#374151] text-white flex items-center justify-center text-xl font-black shadow-lg ring-4 ring-white">
                    H
                </div>
            </div>
        </header>

        <!-- Viewport -->
        <div id="app-viewport" class="flex-1 overflow-y-auto p-12 lg:p-16 scroll-hide">
            <!-- Dynamic Content Injected Here -->
        </div>
    </main>

    <script>
        // --- Data ---
        const units = {
            korean: [
                { 
                    id: 1, 
                    title: '1단원: 생각하며 읽기', 
                    status: 'completed', 
                    desc: '글의 내용을 파악하고 자신의 생각 정리하기',
                    lesson: {
                        passageTitle: "꼬마 여우의 용기 있는 고백",
                        passageText: "햇살이 따뜻하게 내리쬐는 어느 봄날 아침이었습니다. 숲속 마을의 동물 친구들은 모두 광장에 모여 즐겁게 놀고 있었지만, 오직 꼬마 여우 '루루'만이 나무 뒤에 숨어 그 모습을 지켜보고 있었습니다.\n\n사실 루루는 며칠 전, 친구들이 아끼는 꽃밭을 실수로 망가뜨리고 말았습니다. 너무 당황한 나머지 사과도 하지 못하고 도망쳤던 루루는 밤새 잠을 이룰 수 없었습니다. '친구들이 나를 미워하면 어쩌지?'라는 걱정이 앞섰지만, 루루는 용기를 내기로 했습니다.\n\n루루는 정성스럽게 사과 편지를 썼습니다. 자신의 실수를 솔직하게 고백하고, 다시는 그러지 않겠다는 다짐도 담았습니다. 루루가 떨리는 손으로 광장 게시판에 편지를 붙이고 돌아서려 할 때, 토끼 친구가 다가와 루루의 어깨를 토닥였습니다. 진심이 담긴 편지 한 장이 루루와 친구들 사이의 차가운 벽을 허물어뜨린 순간이었습니다.",
                        quiz: [
                            { question: "1. 루루가 밤새 잠을 이루지 못했던 근본적인 이유는 무엇인가요?", options: ["친구가 없어서 심심했기 때문", "실수를 사과하지 못한 미안함 때문", "꽃밭이 너무 예뻐서", "편지를 쓰는 것이 힘들어서"], answer: 1 },
                            { question: "2. 글의 내용으로 보아 진정한 화해를 위해 가장 중요한 것은 무엇일까요?", options: ["비싼 선물", "자신의 실수를 숨기는 것", "솔직한 고백과 진심", "모르는 척 지나가기"], answer: 2 }
                        ]
                    }
                },
                { 
                    id: 2, 
                    title: '2단원: 이야기 속의 인물', 
                    status: 'completed', 
                    desc: '인물의 성격과 행동 이해하기',
                    lesson: {
                        passageTitle: "지혜로운 소년과 욕심쟁이 부자",
                        passageText: "옛날 아주 먼 마을에 한 부자가 살고 있었습니다. 그는 마을에서 가장 큰 집과 많은 곡식을 가졌지만, 마음만은 아주 가난했습니다. 이웃에게 베풀 줄을 몰랐고 늘 더 많은 재물을 탐냈기 때문입니다.\n\n어느 날, 마을의 한 소년이 길에서 금화가 든 주머니를 주웠습니다. 소년은 주저하지 않고 주머니 주인을 찾아 나섰고, 마침내 그것이 부자의 것임을 알게 되었습니다. 주머니를 돌려받은 부자는 고마워하기는커녕 소년에게 상금을 주지 않으려고 거짓말을 했습니다. \"원래 이 주머니에는 금화 열 개가 더 들어있었단다. 네가 가져간 것이 아니냐!\"\n\n마을 사람들은 모두 당황했지만, 소년은 침착했습니다. 소년은 고을의 사또에게 가서 말했습니다. \"사또님, 주인님의 말씀이 맞다면 이 주머니는 주인님의 것이 아닙니다. 주인님은 금화가 열 개 더 든 주머니를 잃어버리셨고, 제가 주운 것은 그보다 적은 금화가 든 주머니니까요.\" 결국 사또는 소년의 지혜로운 말에 감탄하며 부자의 거짓말을 밝혀내고 정직한 소년의 손을 들어주었습니다.",
                        quiz: [
                            { question: "1. 이야기 속 부자의 성격과 거리가 먼 것은?", options: ["이기적이다", "욕심이 많다", "정직하지 못하다", "베풀기를 좋아한다"], answer: 3 },
                            { question: "2. 소년이 부자의 거짓말에 대처한 방법으로 옳은 것은?", options: ["부자에게 화를 내며 싸웠다", "부자의 말을 논리적으로 이용하여 반박했다", "무서워서 금화를 그냥 주고 도망갔다", "마을 사람들에게 부자를 욕했다"], answer: 1 }
                        ]
                    }
                }
            ],
            math: [
                { 
                    id: 1, 
                    title: '1단원: 10, 100의 자리 덧셈', 
                    status: 'completed', 
                    desc: '주산 원리를 이용한 큰 수의 연산 기초',
                    lesson: {
                        passageTitle: "주산의 기초: 자리 잡기와 보수 활용",
                        passageText: "주산은 머릿속에 주판의 이미지를 그리며 계산하는 훌륭한 방법입니다. 먼저 10의 자리와 100의 자리를 더할 때는 자릿값을 정확히 지키는 것이 가장 중요합니다.\n\n예를 들어, 120과 340을 더할 때는 100의 자리 숫자 1과 3을 먼저 더해 4를 만들고, 그다음 10의 자리 숫자 2와 4를 더해 6을 만듭니다. 주판알을 올릴 때는 엄지손가락을, 내릴 때는 집게손가락을 사용하는 규칙을 잊지 마세요.\n\n암산을 할 때도 실제 주판알이 움직이는 모습을 상상해 보세요. 100단위가 넘어가는 연산에서도 자릿수만 잘 맞춘다면 아무리 큰 수도 쉽고 빠르게 계산할 수 있답니다.",
                        quiz: [
                            { question: "1. 120 + 340을 계산할 때 100의 자리 숫자의 합은 얼마인가요?", options: ["2", "3", "4", "5"], answer: 2 },
                            { question: "2. 주판에서 알을 위로 올릴 때 주로 사용하는 손가락은?", options: ["엄지손가락", "검지손가락", "중지손가락", "약지손가락"], answer: 0 }
                        ]
                    }
                },
                { 
                    id: 2, 
                    title: '2단원: 1000의 자리와 소수', 
                    status: 'in-progress', 
                    desc: '네 자릿수 덧셈과 소수의 개념 이해하기',
                    lesson: {
                        passageTitle: "큰 수의 연산과 신비로운 소수의 세계",
                        passageText: "우리가 지금까지 배운 자연수보다 더 큰 '1000의 자리' 연산을 시작해 봅시다. 1000의 자리 덧셈은 받아올림에 주의해야 합니다. 100의 자리의 합이 10이 되면 1000의 자리로 1을 넘겨주는 규칙을 주판에서도 똑같이 적용합니다.\n\n[소수를 먼저 배워요: 새로운 수의 탄생]\n세상에는 1보다 작은 값을 나타내야 할 때가 많습니다. 사과 한 개를 똑같이 열 조각으로 나눈 것 중 한 조각은 어떻게 표현할까요? 바로 이때 '소수'를 사용합니다. 1을 열 개로 나눈 것 중 하나를 0.1이라고 부르기로 약속했습니다.\n\n주판에서는 빨간색 자릿점을 기준으로 왼쪽은 우리가 아는 일, 십, 백, 천의 자리가 되고, 오른쪽은 0.1(소수 첫째 자리), 0.01(소수 둘째 자리)의 위치가 됩니다. 점(.)의 위치를 정확히 확인하는 것이 소수 공부의 핵심입니다!",
                        quiz: [
                            { 
                                question: "1. 1,250 + 2,830을 계산하세요. (받아올림에 주의!)", 
                                options: ["3,080", "4,080", "4,180", "4,280"], 
                                answer: 1 
                            },
                            { 
                                question: "2. 1을 똑같이 10개로 나눈 것 중 하나를 소수로 나타내면?", 
                                options: ["0.01", "0.1", "1.1", "10.0"], 
                                answer: 1 
                            },
                            { 
                                question: "3. 2,000에 0.7을 더하면 어떤 수가 될까요?", 
                                options: ["2,000.7", "2,007", "2,070", "2,700"], 
                                answer: 0 
                            }
                        ]
                    }
                },
            ]
        };

        // --- State Management ---
        let state = {
            currentView: 'dashboard',
            selectedSubject: null,
            selectedUnit: null,
            quizAnswers: {},
            showResults: false,
            isDevMode: false
        };

        const viewport = document.getElementById('app-viewport');
        const loginModal = document.getElementById('login-modal');
        const devPasswordInput = document.getElementById('dev-password');
        const loginError = document.getElementById('login-error');
        const devBadge = document.getElementById('dev-badge');
        const devBtnText = document.getElementById('dev-btn-text');

        // --- Router Logic ---
        const router = {
            go: (view, params = {}) => {
                state.currentView = view;
                if (params.subject) state.selectedSubject = params.subject;
                if (params.unit !== undefined) state.selectedUnit = params.unit;
                
                if (view === 'unit-list' || view === 'download') {
                    state.selectedUnit = null;
                    state.quizAnswers = {};
                    state.showResults = false;
                }

                router.render();
            },
            render: () => {
                viewport.innerHTML = '';
                switch(state.currentView) {
                    case 'dashboard': renderDashboard(); break;
                    case 'unit-list': renderUnitList(); break;
                    case 'lesson': renderLesson(); break;
                    case 'download': renderDownload(); break;
                }
                updateSidebar();
            }
        };

        // --- Login Logic ---
        window.openLoginModal = () => {
            if (state.isDevMode) {
                // 로그아웃 처리
                state.isDevMode = false;
                devBadge.classList.add('hidden');
                devBtnText.innerText = "개발자 로그인";
                return;
            }
            loginModal.classList.remove('hidden');
            devPasswordInput.value = '';
            loginError.classList.add('hidden');
            devPasswordInput.focus();
        };

        window.closeLoginModal = () => {
            loginModal.classList.add('hidden');
        };

        window.attemptLogin = () => {
            if (devPasswordInput.value === '1234') {
                state.isDevMode = true;
                devBadge.classList.remove('hidden');
                devBtnText.innerText = "로그아웃";
                closeLoginModal();
            } else {
                loginError.classList.remove('hidden');
            }
        };

        // Enter key support for login
        devPasswordInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') attemptLogin();
        });

        // --- Render Functions ---
        function renderDashboard() {
            const container = document.createElement('div');
            container.className = 'max-w-5xl mx-auto animate-fade-in';
            container.innerHTML = `
                <div class="text-center mb-20">
                    <h2 class="text-[90px] font-black text-[#374151] mb-8 italic tracking-tighter leading-none">
                        Welcome back!
                    </h2>
                    <p class="text-slate-600 text-2xl font-bold">오늘 관리할 초등 4학년 과목을 선택해주세요.</p>
                </div>
                
                <div class="grid grid-cols-1 md:grid-cols-2 gap-12 px-10">
                    <button onclick="router.go('unit-list', {subject: 'korean'})" class="flex flex-col items-center bg-white/60 backdrop-blur-sm rounded-[3.5rem] p-12 transition-all duration-300 hover:bg-white hover:shadow-2xl hover:-translate-y-2 group border border-white/50">
                        <div class="p-8 rounded-[2.5rem] bg-[#D1FAE5] mb-8 group-hover:scale-105 transition-transform">
                            <i class="fas fa-book-bookmark text-[#059669] text-6xl"></i>
                        </div>
                        <h3 class="text-4xl font-black text-[#374151] mb-4 tracking-tighter">국어 교육</h3>
                        <p class="text-slate-500 text-lg font-bold">문해력과 사고력 확장</p>
                    </button>

                    <button onclick="router.go('unit-list', {subject: 'math'})" class="flex flex-col items-center bg-white/60 backdrop-blur-sm rounded-[3.5rem] p-12 transition-all duration-300 hover:bg-white hover:shadow-2xl hover:-translate-y-2 group border border-white/50">
                        <div class="p-8 rounded-[2.5rem] bg-[#FFEDD5] mb-8 group-hover:scale-105 transition-transform">
                            <i class="fas fa-calculator text-[#EA580C] text-6xl"></i>
                        </div>
                        <h3 class="text-4xl font-black text-[#374151] mb-4 tracking-tighter">수학 교육</h3>
                        <p class="text-slate-500 text-lg font-bold">기초 연산과 주산의 힘</p>
                    </button>
                </div>
            `;
            viewport.appendChild(container);
        }

        function renderUnitList() {
            const subjectData = units[state.selectedSubject];
            const title = state.selectedSubject === 'math' ? '수학 교육 단원' : '국어 교육 단원';
            const colorClass = state.selectedSubject === 'math' ? 'bg-orange-100 text-orange-600' : 'bg-emerald-100 text-emerald-600';

            const container = document.createElement('div');
            container.className = 'max-w-4xl mx-auto animate-fade-in';
            container.innerHTML = `
                <button onclick="router.go('dashboard')" class="text-slate-500 font-black flex items-center mb-10 text-xl group">
                    <span class="mr-3 group-hover:-translate-x-2 transition-transform italic text-2xl">←</span> 과목 선택으로 돌아가기
                </button>
                <h3 class="text-5xl font-black text-slate-800 mb-12 tracking-tight">${title}</h3>
                <div class="grid grid-cols-1 gap-6">
                    ${subjectData.map((unit, idx) => `
                        <button onclick="router.go('lesson', {unit: ${idx}})" class="unit-card bg-white/80 backdrop-blur-sm p-8 rounded-[2.5rem] border border-white transition-all text-left flex items-center group">
                            <div class="p-4 rounded-2xl mr-8 ${colorClass}">
                                <i class="fas fa-chevron-right text-2xl"></i>
                            </div>
                            <div class="flex-1">
                                <div class="flex items-center justify-between mb-2">
                                    <h4 class="text-2xl font-black text-slate-800">${unit.title}</h4>
                                    ${unit.status === 'completed' ? '<i class="fas fa-check-circle text-emerald-500 text-xl"></i>' : '<i class="fas fa-clock text-orange-400 text-xl animate-pulse"></i>'}
                                </div>
                                <p class="text-slate-500 font-bold text-lg">${unit.desc}</p>
                            </div>
                        </button>
                    `).join('')}
                </div>
            `;
            viewport.appendChild(container);
        }

        function renderLesson() {
            const unit = units[state.selectedSubject][state.selectedUnit];
            const themeColor = state.selectedSubject === 'math' ? 'orange' : 'emerald';

            const container = document.createElement('div');
            container.className = 'max-w-4xl mx-auto animate-fade-in space-y-12 pb-24';
            container.innerHTML = `
                <button onclick="router.go('unit-list')" class="text-slate-500 font-black flex items-center text-xl group">
                    <span class="mr-3 group-hover:-translate-x-2 transition-transform italic text-2xl">←</span> 단원 선택으로 돌아가기
                </button>

                <div class="bg-white p-14 rounded-[3.5rem] shadow-xl border border-slate-100 relative overflow-hidden">
                    <h4 class="text-3xl font-black mb-10 text-center py-8 rounded-[2rem] bg-${themeColor}-50 text-${themeColor}-700">
                        ${unit.lesson.passageTitle}
                    </h4>
                    <div class="text-slate-700 font-bold text-xl leading-[2.2] indent-8 whitespace-pre-line bg-slate-50/50 p-8 rounded-[2rem] border border-slate-50">
                        ${unit.lesson.passageText}
                    </div>
                </div>

                <div class="bg-white p-14 rounded-[3.5rem] shadow-xl border border-slate-100 space-y-10">
                    <div class="flex items-center justify-between">
                        <h4 class="text-3xl font-black text-slate-800 flex items-center space-x-3">
                            <i class="fas fa-book-open-reader text-${themeColor}-500"></i>
                            <span>실력 확인 퀴즈</span>
                        </h4>
                    </div>

                    <div class="space-y-10">
                        ${unit.lesson.quiz.map((q, qIdx) => renderQuizItemMarkup(q, qIdx)).join('')}
                    </div>

                    ${!state.showResults ? `
                        <div class="text-center pt-6">
                            <button onclick="handleSubmitQuiz()" class="px-16 py-6 rounded-3xl font-black text-2xl text-white transition-all active:scale-95 bg-${themeColor === 'orange' ? 'orange-500 hover:bg-orange-600' : 'slate-900 hover:bg-slate-800'} shadow-2xl">
                                과제 제출하고 정답 확인하기
                            </button>
                        </div>
                    ` : `
                        <div class="text-center pt-6">
                            <button onclick="router.go('unit-list')" class="px-12 py-5 rounded-2xl font-black text-lg bg-slate-100 text-slate-600 hover:bg-slate-200 transition-colors">
                                단원 목록으로 돌아가기
                            </button>
                        </div>
                    `}
                </div>
            `;
            viewport.appendChild(container);
        }

        function renderDownload() {
            const container = document.createElement('div');
            container.className = 'max-w-4xl mx-auto animate-fade-in';
            container.innerHTML = `
                <div class="text-center mb-16">
                    <h3 class="text-5xl font-black text-slate-800 mb-6 tracking-tight">학습지 자료실</h3>
                    <p class="text-slate-500 text-xl font-bold">필요한 단원의 학습지를 클릭하여 확인해보세요.</p>
                </div>
                
                <div class="grid grid-cols-1 gap-4">
                    <div class="bg-white p-8 rounded-[2rem] border border-slate-100 flex items-center justify-between hover:shadow-lg transition-all">
                        <div class="flex items-center space-x-6">
                            <div class="bg-emerald-100 p-4 rounded-2xl text-emerald-600">
                                <i class="fas fa-file-pdf text-2xl"></i>
                            </div>
                            <div>
                                <h4 class="text-xl font-black text-slate-800">4학년 국어 1단원 보충 활동지</h4>
                                <p class="text-slate-400 font-bold text-sm">구글 프레젠테이션 활동지</p>
                            </div>
                        </div>
                        <button onclick="window.open('https://docs.google.com/presentation/d/14sLzWnsByO-_lE3V_U1JVj7LTUlXC0wjuULirLL0EVA/edit?slide=id.p#slide=id.p', '_blank')" class="bg-slate-900 text-white px-8 py-3 rounded-xl font-black hover:bg-slate-800 transition-colors">다운로드</button>
                    </div>

                    <div class="bg-white p-8 rounded-[2rem] border border-slate-100 flex items-center justify-between hover:shadow-lg transition-all">
                        <div class="flex items-center space-x-6">
                            <div class="bg-orange-100 p-4 rounded-2xl text-orange-600">
                                <i class="fas fa-file-pdf text-2xl"></i>
                            </div>
                            <div>
                                <h4 class="text-xl font-black text-slate-800">4학년 수학 주산 1000의자리 연습장</h4>
                                <p class="text-slate-400 font-bold text-sm">구글 프레젠테이션 연습장</p>
                            </div>
                        </div>
                        <button onclick="window.open('https://docs.google.com/presentation/d/1-znMb7o8JNR4S_0A7Z2WbVAyjLowdQTYhrOO0M4l8Yw/edit?slide=id.g3d885b62350_0_143#slide=id.g3d885b62350_0_143', '_blank')" class="bg-slate-900 text-white px-8 py-3 rounded-xl font-black hover:bg-slate-800 transition-colors">다운로드</button>
                    </div>

                    <div class="bg-white p-8 rounded-[2rem] border border-slate-100 flex items-center justify-between hover:shadow-lg transition-all opacity-50">
                        <div class="flex items-center space-x-6">
                            <div class="bg-slate-100 p-4 rounded-2xl text-slate-400">
                                <i class="fas fa-lock text-2xl"></i>
                            </div>
                            <div>
                                <h4 class="text-xl font-black text-slate-800">기말고사 대비 통합 문제집 (준비중)</h4>
                                <p class="text-slate-400 font-bold text-sm">COMING SOON</p>
                            </div>
                        </div>
                        <button disabled class="bg-slate-200 text-slate-400 px-8 py-3 rounded-xl font-black cursor-not-allowed">잠김</button>
                    </div>
                </div>
            `;
            viewport.appendChild(container);
        }

        function renderQuizItemMarkup(q, qIdx) {
            const themeColor = state.selectedSubject === 'math' ? 'orange' : 'emerald';
            let containerClass = "bg-slate-50/50 border-slate-100";
            if (state.showResults) {
                containerClass = state.quizAnswers[qIdx] === q.answer ? "bg-emerald-50 border-emerald-200" : "bg-rose-50 border-rose-200";
            }

            return `
                <div class="p-10 rounded-[2.5rem] transition-all border-2 ${containerClass}">
                    <div class="flex items-start space-x-4 mb-6">
                        <span class="w-8 h-8 rounded-full flex items-center justify-center text-white font-black shrink-0 bg-${themeColor}-400">${qIdx + 1}</span>
                        <p class="text-xl font-black text-slate-800 leading-tight">${q.question}</p>
                    </div>
                    
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        ${q.options.map((opt, oIdx) => {
                            const isSelected = state.quizAnswers[qIdx] === oIdx;
                            const isCorrect = q.answer === oIdx;
                            
                            let btnClass = "bg-white border-slate-100 text-slate-500 hover:border-slate-300";
                            if (state.showResults) {
                                if (isCorrect) btnClass = "bg-emerald-500 border-emerald-500 text-white shadow-lg";
                                else if (isSelected && !isCorrect) btnClass = "bg-rose-500 border-rose-500 text-white shadow-lg";
                                else btnClass = "bg-white border-slate-100 text-slate-300 opacity-60";
                            } else if (isSelected) {
                                btnClass = `bg-white border-${themeColor}-500 text-${themeColor}-600 shadow-md ring-2 ring-${themeColor}-100`;
                            }

                            return `
                                <button onclick="handleAnswerSelect(${qIdx}, ${oIdx})" ${state.showResults ? 'disabled' : ''} class="p-6 rounded-2xl text-left font-bold transition-all border-2 flex items-center justify-between ${btnClass}">
                                    <span>${oIdx + 1}. ${opt}</span>
                                    ${state.showResults && isCorrect ? '<i class="fas fa-check-circle"></i>' : ''}
                                    ${state.showResults && isSelected && !isCorrect ? '<i class="fas fa-exclamation-circle"></i>' : ''}
                                </button>
                            `;
                        }).join('')}
                    </div>
                </div>
            `;
        }

        // --- Interaction Handlers ---
        window.handleAnswerSelect = (qIndex, optIndex) => {
            if (state.showResults) return;
            state.quizAnswers[qIndex] = optIndex;
            router.render();
        };

        window.handleSubmitQuiz = () => {
            state.showResults = true;
            router.render();
        };

        function updateSidebar() {
            const dashBtn = document.getElementById('nav-dashboard');
            const downBtn = document.getElementById('nav-download');
            
            if (state.currentView === 'dashboard' || state.currentView === 'unit-list' || state.currentView === 'lesson') {
                dashBtn.className = 'w-full flex items-center space-x-4 px-6 py-5 rounded-[1.5rem] transition-all bg-orange-50 text-orange-600 font-black';
                downBtn.className = 'w-full flex items-center space-x-4 px-6 py-5 rounded-[1.5rem] text-slate-400 font-bold hover:bg-white transition-all';
            } else if (state.currentView === 'download') {
                downBtn.className = 'w-full flex items-center space-x-4 px-6 py-5 rounded-[1.5rem] transition-all bg-orange-50 text-orange-600 font-black';
                dashBtn.className = 'w-full flex items-center space-x-4 px-6 py-5 rounded-[1.5rem] text-slate-400 font-bold hover:bg-white transition-all';
            }
        }

        // --- Init ---
        window.onload = () => {
            router.render();
        };
    </script>
</body>
</html>
