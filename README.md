
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>하시교육 관리 시스템</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@400;700;900&display=swap');
        body { font-family: 'Noto Sans KR', sans-serif; background-color: #F1F3F5; }
        .glass-card { background: rgba(255, 255, 255, 0.7); backdrop-filter: blur(12px); border: 1px solid rgba(255, 255, 255, 0.5); }
        .scroll-hide::-webkit-scrollbar { display: none; }
        .animate-fade-in { animation: fadeIn 0.4s ease-out forwards; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .modal-overlay { background: rgba(0, 0, 0, 0.4); backdrop-filter: blur(4px); }
        button, input { touch-action: manipulation; }
    </style>
</head>
<body class="min-h-screen flex overflow-hidden">

    <!-- 개발자 로그인 모달 -->
    <div id="login-modal" class="hidden fixed inset-0 z-50 flex items-center justify-center modal-overlay p-4">
        <div class="bg-white rounded-[2.5rem] p-8 md:p-10 w-full max-w-md shadow-2xl border border-slate-100 animate-fade-in">
            <h3 class="text-2xl font-black text-slate-800 mb-6 text-center">개발자 인증</h3>
            <input type="password" id="dev-password" placeholder="비밀번호" class="w-full px-6 py-4 rounded-2xl bg-slate-100 border-none mb-4 text-center tracking-widest outline-none focus:ring-2 focus:ring-orange-300 transition-all">
            <p id="login-error" class="text-rose-500 text-sm font-bold text-center hidden mb-4">비밀번호가 틀렸습니다.</p>
            <div class="flex space-x-3">
                <button onclick="closeLoginModal()" class="flex-1 py-4 rounded-2xl bg-slate-100 font-black hover:bg-slate-200 transition-colors">취소</button>
                <button onclick="attemptLogin()" class="flex-1 py-4 rounded-2xl bg-orange-500 text-white font-black hover:bg-orange-600 transition-colors shadow-lg shadow-orange-100">인증</button>
            </div>
        </div>
    </div>

    <!-- 사이드바 -->
    <aside class="hidden md:flex w-80 glass-card flex-col border-r border-slate-200 h-screen shrink-0">
        <div class="p-10 mb-6">
            <div class="flex items-center space-x-4">
                <div class="bg-orange-500 p-3 rounded-2xl shadow-lg shadow-orange-200">
                    <svg class="w-5 h-5 text-white" viewBox="0 0 24 24" fill="currentColor" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polygon points="12 2 15.09 8.26 22 9.27 17 14.14 18.18 21.02 12 17.77 5.82 21.02 7 14.14 2 9.27 8.91 8.26 12 2"/></svg>
                </div>
                <div>
                    <h1 class="text-2xl font-black text-slate-800 tracking-tighter leading-none">하시교육</h1>
                    <p class="text-[10px] text-orange-600 font-black tracking-widest mt-2">4학년 교육 · 주산 전문</p>
                </div>
            </div>
        </div>
        <nav class="flex-1 px-6 space-y-2">
            <!-- 1. 과목 선택 -->
            <button onclick="router.go('dashboard')" id="nav-dashboard" class="w-full flex items-center space-x-4 px-6 py-5 rounded-2xl font-black transition-all">
                <svg class="w-6 h-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect width="7" height="9" x="3" y="3" rx="1"/><rect width="7" height="5" x="14" y="3" rx="1"/><rect width="7" height="9" x="14" y="12" rx="1"/><rect width="7" height="5" x="3" y="16" rx="1"/></svg>
                <span>과목 선택</span>
            </button>
            <!-- 2. 게임 센터 (사이드바에 정식 추가) -->
            <button onclick="router.go('game')" id="nav-game" class="w-full flex items-center space-x-4 px-6 py-5 rounded-2xl font-black transition-all">
                <svg class="w-6 h-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="6" x2="10" y1="12" y2="12"/><line x1="8" x2="8" y1="10" y2="14"/><line x1="15" x2="15.01" y1="13" y2="13"/><line x1="18" x2="18.01" y1="11" y2="11"/><rect width="20" height="12" x="2" y="6" rx="2"/></svg>
                <span>게임 센터</span>
            </button>
            <!-- 3. 학습지 자료실 -->
            <button onclick="router.go('download')" id="nav-download" class="w-full flex items-center space-x-4 px-6 py-5 rounded-2xl font-black transition-all">
                <svg class="w-6 h-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="7 10 12 15 17 10"/><line x1="12" x2="12" y1="15" y2="3"/></svg>
                <span>학습지 자료실</span>
            </button>
        </nav>
        <div class="p-10">
            <button onclick="openLoginModal()" class="group text-slate-400 font-bold hover:text-orange-500 flex items-center space-x-2 transition-colors">
                <span id="dev-icon-container">
                    <svg id="dev-icon-code" class="w-5 h-5 group-hover:rotate-12 transition-transform" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="16 18 22 12 16 6"/><polyline points="8 6 2 12 8 18"/></svg>
                </span>
                <span id="dev-btn-text">개발자 로그인</span>
            </button>
        </div>
    </aside>

    <!-- 메인 영역 -->
    <main class="flex-1 flex flex-col h-screen overflow-hidden">
        <header class="h-20 px-6 md:px-12 flex items-center justify-between shrink-0 bg-white/50 backdrop-blur-sm">
            <div class="glass-card px-4 py-2 rounded-full flex items-center space-x-3">
                <span class="text-[10px] font-black text-slate-400 uppercase tracking-widest">Admin Center</span>
                <div id="dev-badge" class="hidden px-3 py-0.5 bg-orange-500 text-white text-[10px] font-black rounded-lg animate-pulse">DEV MODE</div>
            </div>
            <div class="flex items-center space-x-4">
                <span class="hidden md:inline text-sm font-bold text-slate-500">2024년 4학년 과정</span>
                <div class="w-10 h-10 rounded-xl bg-slate-800 text-white flex items-center justify-center font-black shadow-lg">H</div>
            </div>
        </header>
        <div id="app-viewport" class="flex-1 overflow-y-auto p-6 md:p-12 scroll-hide"></div>
    </main>

    <script>
        // 데이터 구조 정의 (수학 3단원 추가)
        const units = {
            korean: [
                { 
                    id: 1, 
                    title: '1단원: 생각하며 읽기', 
                    lesson: { 
                        passageTitle: "꼬마 여우의 용기 있는 고백", 
                        passageText: "햇살이 따뜻하게 내리쬐는 어느 봄날이었습니다. 꼬마 여우 루루는 친구들이 아끼는 꽃밭을 실수로 망가뜨렸습니다.\n루루는 마음이 너무 무거워서 밤새 잠을 이루지 못했습니다. 고민 끝에 루루는 진심을 담은 사과 편지를 쓰기로 했습니다. 잘못을 인정하는 것은 무섭지만, 친구와의 우정이 더 소중했기 때문입니다.", 
                        quiz: [
                            { question: "루루가 사과하기 위해 선택한 방법은?", options: ["모르는 척하기", "편지 쓰기", "꽃 사오기", "도망가기"], answer: 1 },
                            { question: "날씨는 어떠했나요?", options: ["비가 왔다", "눈이 내렸다", "따뜻했다", "태풍이 불었다"], answer: 2 },
                            { question: "꼬마 여우 루루의 마음 상태를 가장 잘 나타낸 것은?", options: ["신이 났다", "마음이 무거웠다", "아무렇지 않았다", "화가 났다"], answer: 1 }
                        ] 
                    } 
                },
                { 
                    id: 2, 
                    title: '2단원: 내용을 간추려요', 
                    lesson: { 
                        passageTitle: "우리 몸을 지키는 손 씻기", 
                        passageText: "우리의 손에는 눈에 보이지 않는 수많은 세균이 살고 있습니다. 외출 후나 음식을 먹기 전에는 반드시 비누를 사용하여 30초 이상 깨끗이 손을 씻어야 합니다.\n올바른 손 씻기는 감기나 식중독 같은 질병을 예방하는 가장 쉽고 좋은 방법입니다.", 
                        quiz: [
                            { question: "이 글의 중심 소재는 무엇인가요?", options: ["비누의 종류", "손 씻기의 중요성", "세균의 번식", "병원의 위치"], answer: 1 },
                            { question: "손을 씻어야 하는 가장 큰 이유는?", options: ["예뻐지려고", "질병을 예방하려고", "향기 때문에", "그냥"], answer: 1 },
                            { question: "손을 씻을 때 가장 바람직한 방법은?", options: ["물로만 대충 씻는다", "비누를 사용해 30초 이상 씻는다", "수건으로 닦기만 한다", "옷에 문질러 닦는다"], answer: 1 }
                        ] 
                    } 
                },
                { 
                    id: 3, 
                    title: '3단원: 줄임말을 줄여요', 
                    lesson: { 
                        passageTitle: "아름다운 우리말 사용하기", 
                        passageText: "요즘 우리 주변에서는 말을 줄여서 쓰는 모습을 자주 볼 수 있습니다. 예를 들어, '삼각김밥'을 '삼김'이라고 부르거나, '열심히 공부하자'를 '열공'이라고 짧게 줄여 말하곤 합니다.\n하지만 줄임말을 너무 많이 사용하면 뜻을 이해하지 못해 대화가 어려워질 수 있고, 우리말의 아름다움이 훼손될 수 있습니다.", 
                        quiz: [
                            { question: "'삼김'은 어떤 낱말을 줄인 것인가요?", options: ["삼중 김밥", "삼각 김밥", "삼성 김밥", "삼색 김밥"], answer: 1 },
                            { question: "'열심히 공부하자'를 줄인 말은 무엇인가요?", options: ["열공", "공열", "열심", "공부"], answer: 0 },
                            { question: "줄임말을 너무 많이 사용할 때 생길 수 있는 문제점은?", options: ["글씨 쓰기가 편해진다", "대화가 어려워질 수 있다", "친구들이 좋아한다", "우리말이 더 아름다워진다"], answer: 1 }
                        ] 
                    } 
                }
            ],
            math: [
                { 
                    id: 1, 
                    title: '1단원: 1000의 자리 덧셈', 
                    lesson: { 
                        passageTitle: "주산 암산: 1000의 자리 기초", 
                        passageText: "1000의 자리 연산은 자릿수를 잘 맞추고 받아올림에 주의해야 합니다.\n100의 자리의 합이 10이 되거나 넘어가면 1000의 자리로 1을 넘겨주는 규칙을 주판에서도 똑같이 적용합니다.", 
                        quiz: [
                            { question: "1,250 + 2,830을 계산하세요.", options: ["3,080", "4,080", "4,180", "3,980"], answer: 1 },
                            { question: "주판에서 백의 자리가 10이 되면 천의 자리에 몇을 더하나요?", options: ["1", "5", "10", "더하지 않는다"], answer: 0 },
                            { question: "3,500 + 4,600을 바르게 계산한 값은?", options: ["7,100", "8,100", "8,000", "7,000"], answer: 1 }
                        ] 
                    } 
                },
                { 
                    id: 2, 
                    title: '2단원: 소수를 배워요', 
                    lesson: { 
                        passageTitle: "새로운 수의 탄생: 소수", 
                        passageText: "1을 똑같이 10개로 나눈 것 중 하나를 0.1이라고 부르기로 약속했습니다. 이를 소수라고 부릅니다.\n주판에서는 자릿점(빨간색 점)을 기준으로 왼쪽은 자연수(일, 십, 백...), 오른쪽은 소수(0.1, 0.01...) 자리가 됩니다.", 
                        quiz: [
                            { question: "1을 똑같이 10개로 나눈 것 중 하나를 소수로 나타내면?", options: ["0.01", "0.1", "1.0", "0.001"], answer: 1 },
                            { question: "2,000에 0.7을 더하면 어떤 수가 될까요?", options: ["2,000.7", "2,007", "20.7", "2.0007"], answer: 0 },
                            { question: "0.1이 5개 모이면 얼마가 될까요?", options: ["0.05", "0.5", "5", "50"], answer: 1 }
                        ] 
                    } 
                },
                { 
                    id: 3, 
                    title: '3단원: 각도와 삼각형', 
                    lesson: { 
                        passageTitle: "도형의 기초: 삼각형의 성질", 
                        passageText: "삼각형은 세 개의 변과 세 개의 각으로 이루어진 도형입니다.\n어떤 모양의 삼각형이든 세 내각을 모두 더하면 항상 180도가 됩니다. 그중 한 각이 90도(직각)인 삼각형을 직각삼각형이라고 부릅니다.", 
                        quiz: [
                            { question: "삼각형의 세 각을 모두 더하면 몇 도인가요?", options: ["90도", "180도", "270도", "360도"], answer: 1 },
                            { question: "직각은 몇 도를 의미하나요?", options: ["45도", "60도", "90도", "120도"], answer: 2 },
                            { question: "정삼각형의 한 각의 크기는 몇 도일까요?", options: ["45도", "60도", "90도", "120도"], answer: 1 }
                        ] 
                    } 
                }
            ]
        };

        // 앱 상태 관리
        let state = { 
            currentView: 'dashboard', 
            selectedSubject: null, 
            selectedUnit: null, 
            quizAnswers: {}, 
            showResults: false, 
            isDevMode: false,
            gameState: { targetNum: 0, attempts: 0, message: "", isOver: false }
        };

        const viewport = document.getElementById('app-viewport');

        // 라우터 시스템
        const router = {
            go: (view, params = {}) => {
                state.currentView = view;
                if (params.subject) state.selectedSubject = params.subject;
                if (params.unit !== undefined) state.selectedUnit = params.unit;
                
                if (view !== 'lesson') { 
                    state.quizAnswers = {}; 
                    state.showResults = false; 
                }
                if (view === 'game') resetGame();
                
                router.render();
            },
            render: () => {
                viewport.innerHTML = '';
                const content = document.createElement('div');
                content.className = "animate-fade-in";
                
                switch(state.currentView) {
                    case 'dashboard': renderDashboard(content); break;
                    case 'unit-list': renderUnitList(content); break;
                    case 'lesson': renderLesson(content); break;
                    case 'download': renderDownload(content); break;
                    case 'game': renderGame(content); break;
                }
                viewport.appendChild(content);
                updateSidebar();
            }
        };

        function renderDashboard(container) {
            container.innerHTML = `
                <div class="max-w-6xl mx-auto text-center">
                    <h2 class="text-[40px] md:text-[80px] font-black text-slate-800 mb-8 md:mb-12 italic tracking-tighter leading-none px-4">Welcome back!</h2>
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-6 md:gap-8 px-4">
                        <button onclick="router.go('unit-list', {subject:'korean'})" class="bg-white p-8 md:p-10 rounded-[2.5rem] md:rounded-[3.5rem] shadow-sm hover:shadow-2xl transition-all border border-white flex flex-col items-center group">
                            <div class="w-20 h-20 md:w-24 md:h-24 rounded-3xl bg-emerald-50 flex items-center justify-center mb-6 group-hover:scale-110 transition-transform">
                                <svg class="w-12 h-12 text-emerald-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M4 19.5v-15A2.5 2.5 0 0 1 6.5 2H20v20H6.5a2.5 2.5 0 0 1 0-5H20"/></svg>
                            </div>
                            <span class="text-2xl md:text-3xl font-black">국어 교육</span>
                        </button>
                        <button onclick="router.go('unit-list', {subject:'math'})" class="bg-white p-8 md:p-10 rounded-[2.5rem] md:rounded-[3.5rem] shadow-sm hover:shadow-2xl transition-all border border-white flex flex-col items-center group">
                            <div class="w-20 h-20 md:w-24 md:h-24 rounded-3xl bg-orange-50 flex items-center justify-center mb-6 group-hover:scale-110 transition-transform">
                                <svg class="w-12 h-12 text-orange-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect width="16" height="20" x="4" y="2" rx="2"/><line x1="8" x2="16" y1="6" y2="6"/><line x1="16" x2="16.01" y1="14" y2="14"/><line x1="12" x2="12.01" y1="14" y2="14"/><line x1="8" x2="8.01" y1="14" y2="14"/><line x1="16" x2="16.01" y1="10" y2="10"/><line x1="12" x2="12.01" y1="10" y2="10"/><line x1="8" x2="8.01" y1="10" y2="10"/><line x1="16" x2="16.01" y1="18" y2="18"/><line x1="12" x2="12.01" y1="18" y2="18"/><line x1="8" x2="8.01" y1="18" y2="18"/></svg>
                            </div>
                            <span class="text-2xl md:text-3xl font-black">수학 교육</span>
                        </button>
                        <button onclick="router.go('game')" class="bg-white p-8 md:p-10 rounded-[2.5rem] md:rounded-[3.5rem] shadow-sm hover:shadow-2xl transition-all border border-white flex flex-col items-center group">
                            <div class="w-20 h-20 md:w-24 md:h-24 rounded-3xl bg-indigo-50 flex items-center justify-center mb-6 group-hover:scale-110 transition-transform">
                                <svg class="w-12 h-12 text-indigo-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="6" x2="10" y1="12" y2="12"/><line x1="8" x2="8" y1="10" y2="14"/><line x1="15" x2="15.01" y1="13" y2="13"/><line x1="18" x2="18.01" y1="11" y2="11"/><rect width="20" height="12" x="2" y="6" rx="2"/></svg>
                            </div>
                            <span class="text-2xl md:text-3xl font-black">게임 센터</span>
                        </button>
                    </div>
                </div>`;
        }

        function renderUnitList(container) {
            const data = units[state.selectedSubject];
            if (!data) { router.go('dashboard'); return; }
            
            container.innerHTML = `
                <div class="max-w-4xl mx-auto">
                    <button onclick="router.go('dashboard')" class="mb-8 font-black text-slate-400 hover:text-slate-600 transition-colors">← 과목 선택으로</button>
                    <h3 class="text-3xl font-black mb-8 text-slate-800">${state.selectedSubject === 'korean' ? '국어' : '수학'} 단원 목록</h3>
                    <div class="space-y-4">
                        ${data.map((u, i) => `
                            <button onclick="router.go('lesson', {unit:${i}})" class="w-full bg-white p-6 md:p-8 rounded-[2rem] text-left border border-white shadow-sm hover:shadow-lg transition-all flex justify-between items-center group">
                                <div>
                                    <span class="text-xs font-black text-orange-400 uppercase tracking-widest mb-1 block">UNIT 0${i+1}</span>
                                    <h4 class="text-xl font-black group-hover:text-orange-500 transition-colors">${u.title}</h4>
                                </div>
                                <svg class="w-5 h-5 text-slate-300 group-hover:text-orange-500 group-hover:translate-x-2 transition-all" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m9 18 6-6-6-6"/></svg>
                            </button>
                        `).join('')}
                    </div>
                </div>`;
        }

        function renderLesson(container) {
            const subjectData = units[state.selectedSubject];
            const unit = subjectData ? subjectData[state.selectedUnit] : null;
            if (!unit) { router.go('unit-list'); return; }

            const totalQuestions = unit.lesson.quiz.length;
            const answeredQuestions = Object.keys(state.quizAnswers).length;
            const isAllAnswered = answeredQuestions === totalQuestions;

            let quizHtml = unit.lesson.quiz.map((q, qIdx) => `
                <div class="p-6 md:p-8 rounded-[2rem] bg-slate-50 border-2 ${state.showResults ? (state.quizAnswers[qIdx] === q.answer ? 'border-emerald-200 bg-emerald-50/30' : 'border-rose-100 bg-rose-50/30') : 'border-slate-100'} mb-6 transition-all">
                    <div class="flex items-start space-x-3 mb-6">
                        <span class="bg-slate-800 text-white w-8 h-8 rounded-lg flex items-center justify-center font-black shrink-0">${qIdx+1}</span>
                        <p class="font-black text-lg md:text-xl text-slate-700 pt-1">${q.question}</p>
                    </div>
                    <div class="grid gap-3">
                        ${q.options.map((opt, oIdx) => {
                            const isSelected = state.quizAnswers[qIdx] === oIdx;
                            const isCorrect = q.answer === oIdx;
                            let btnStyle = "bg-white border-slate-200 text-slate-600 hover:border-orange-300";
                            
                            if (state.showResults) {
                                if (isCorrect) btnStyle = "bg-emerald-500 text-white border-emerald-500 shadow-lg shadow-emerald-100";
                                else if (isSelected) btnStyle = "bg-rose-500 text-white border-rose-500 shadow-lg shadow-rose-100";
                                else btnStyle = "bg-white border-slate-100 text-slate-300 opacity-50";
                            } else if (isSelected) {
                                btnStyle = "border-orange-500 ring-4 ring-orange-100 text-orange-600 bg-orange-50/50";
                            }
                            
                            return `<button onclick="selectAns(${qIdx}, ${oIdx})" class="p-4 md:p-5 rounded-2xl border-2 text-left font-bold transition-all ${btnStyle}">${opt}</button>`;
                        }).join('')}
                    </div>
                    ${state.showResults && state.quizAnswers[qIdx] !== q.answer ? `
                        <div class="mt-4 p-4 bg-rose-50 text-rose-600 rounded-xl font-bold text-sm flex items-center">
                            <svg class="w-4 h-4 mr-2 inline-block shrink-0" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"/><line x1="12" x2="12" y1="8" y2="12"/><line x1="12" x2="12.01" y1="16" y2="16"/></svg>
                            정답은 ${q.answer + 1}번 입니다. 지문을 다시 꼼꼼히 읽어보세요!
                        </div>
                    ` : ''}
                </div>`).join('');

            let submitBtnClass = "";
            let submitBtnText = "";
            if (state.showResults) {
                submitBtnClass = "bg-slate-800 text-white hover:bg-slate-700 shadow-2xl";
                submitBtnText = "다시 풀기";
            } else if (isAllAnswered) {
                submitBtnClass = "bg-orange-500 text-white hover:bg-orange-600 shadow-2xl shadow-orange-200 cursor-pointer animate-pulse";
                submitBtnText = "정답 확인하기";
            } else {
                submitBtnClass = "bg-slate-200 text-slate-400 cursor-not-allowed";
                submitBtnText = `모든 문제를 풀어주세요 (${answeredQuestions}/${totalQuestions})`;
            }

            container.innerHTML = `
                <div class="max-w-4xl mx-auto">
                    <div class="flex justify-between items-center mb-8">
                        <button onclick="router.go('unit-list')" class="font-black text-slate-400 hover:text-slate-600 transition-colors">← 목록으로</button>
                        <span class="bg-white px-4 py-2 rounded-full text-xs font-black text-slate-400 border border-slate-100">${unit.title}</span>
                    </div>
                    <div class="bg-white p-8 md:p-12 rounded-[3rem] shadow-xl mb-8 border border-white">
                        <h3 class="text-2xl md:text-3xl font-black mb-8 text-center text-slate-800 leading-tight">
                            <span class="bg-orange-100 text-orange-600 px-4 py-1 rounded-xl text-sm mb-4 inline-block">본문 읽기</span><br>
                            ${unit.lesson.passageTitle}
                        </h3>
                        <div class="text-lg md:text-xl leading-relaxed text-slate-600 font-medium space-y-4">
                            ${unit.lesson.passageText.split('\n').map(p => `<p>${p}</p>`).join('')}
                        </div>
                    </div>
                    <div class="bg-white p-8 md:p-10 rounded-[3rem] shadow-xl border border-white">
                        <h4 class="text-2xl font-black mb-8 flex items-center text-slate-800">
                            <svg class="w-6 h-6 mr-3 text-orange-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M11 21h2l5.5-5.5a1.5 1.5 0 0 0-2.12-2.12L11 19Z"/><path d="M13 19V9l3.5-3.5a2.83 2.83 0 1 0-4-4L9 5v10"/><circle cx="11" cy="11" r="2"/></svg>
                            학습 확인 퀴즈
                        </h4>
                        ${quizHtml}
                        <button onclick="submitQuiz(${isAllAnswered})" class="w-full py-5 rounded-[1.5rem] font-black text-xl mt-4 transition-all active:scale-[0.98] ${submitBtnClass}">
                            ${submitBtnText}
                        </button>
                    </div>
                </div>`;
        }

        function renderDownload(container) {
            container.innerHTML = `
                <div class="max-w-4xl mx-auto text-center">
                    <div class="w-20 h-20 bg-orange-100 rounded-[2rem] flex items-center justify-center mx-auto mb-6">
                        <svg class="w-8 h-8 text-orange-500" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m6 14 1.45-2.9A2 2 0 0 1 9.24 10H20a2 2 0 0 1 1.94 2.5l-1.55 6a2 2 0 0 1-1.94 1.5H4a2 2 0 0 1-2-2V5c0-1.1.9-2 2-2h3.93a2 2 0 0 1 1.66.9l.82 1.2a2 2 0 0 0 1.66.9H18a2 2 0 0 1 2 2v2"/></svg>
                    </div>
                    <h3 class="text-4xl font-black mb-2 text-slate-800">자료실</h3>
                    <p class="text-slate-400 font-bold mb-12">선생님이 준비한 추가 학습 자료입니다.</p>
                    <div class="grid gap-6 text-left">
                        <div class="bg-white p-8 rounded-[2.5rem] flex flex-col md:flex-row justify-between items-center border border-slate-100 shadow-sm hover:shadow-lg transition-all">
                            <div class="mb-4 md:mb-0">
                                <span class="bg-orange-100 text-orange-600 px-3 py-1 rounded-lg text-xs font-black">MATH</span>
                                <h4 class="text-xl font-black mt-2 text-slate-700">주산 1000의 자리 마스터 연습장</h4>
                                <p class="text-sm text-slate-400 font-medium mt-1 italic">PDF 형태의 출력용 학습지입니다.</p>
                            </div>
                            <button onclick="window.open('https://docs.google.com/presentation/d/1-znMb7o8JNR4S_0A7Z2WbVAyjLowdQTYhrOO0M4l8Yw/edit')" class="w-full md:w-auto bg-orange-500 text-white px-8 py-4 rounded-2xl font-black shadow-lg shadow-orange-100 hover:bg-orange-600 transition-colors">다운로드</button>
                        </div>
                        <div class="bg-white p-8 rounded-[2.5rem] flex flex-col md:flex-row justify-between items-center border border-slate-100 shadow-sm hover:shadow-lg transition-all">
                            <div class="mb-4 md:mb-0">
                                <span class="bg-emerald-100 text-emerald-600 px-3 py-1 rounded-lg text-xs font-black">KOREAN</span>
                                <h4 class="text-xl font-black mt-2 text-slate-700">국어 단원별 핵심 요약 및 평가지</h4>
                                <p class="text-sm text-slate-400 font-medium mt-1 italic">시험 대비 핵심 정리 자료입니다.</p>
                            </div>
                            <button onclick="window.open('https://docs.google.com/presentation/d/14sLzWnsByO-_lE3V_U1JVj7LTUlXC0wjuULirLL0EVA/edit')" class="w-full md:w-auto bg-slate-800 text-white px-8 py-4 rounded-2xl font-black shadow-lg hover:bg-slate-700 transition-colors">다운로드</button>
                        </div>
                    </div>
                </div>`;
        }

        // 게임 로직
        function resetGame() {
            state.gameState = {
                targetNum: Math.floor(Math.random() * 100) + 1,
                attempts: 0,
                message: "1부터 100 사이의 숫자를 맞춰보세요!",
                isOver: false
            };
        }

        window.makeGuess = () => {
            const input = document.getElementById('game-input');
            const val = parseInt(input.value);
            if (isNaN(val) || val < 1 || val > 100) return;

            state.gameState.attempts++;
            if (val === state.gameState.targetNum) {
                state.gameState.message = `정답입니다! 🎉 ${state.gameState.attempts}번 만에 맞추셨어요!`;
                state.gameState.isOver = true;
            } else if (val < state.gameState.targetNum) {
                state.gameState.message = `UP! ${val}보다 더 큰 숫자예요.`;
            } else {
                state.gameState.message = `DOWN! ${val}보다 더 작은 숫자예요.`;
            }
            input.value = '';
            router.render();
        };

        function renderGame(container) {
            container.innerHTML = `
                <div class="max-w-2xl mx-auto text-center">
                    <button onclick="router.go('dashboard')" class="mb-8 font-black text-slate-400 block text-left">← 뒤로가기</button>
                    <div class="bg-white p-10 md:p-12 rounded-[4rem] shadow-2xl border-4 border-indigo-50">
                        <div class="w-20 h-20 bg-indigo-500 rounded-[2rem] flex items-center justify-center mx-auto mb-8 shadow-xl shadow-indigo-100">
                            <svg class="w-10 h-10 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M12 5a3 3 0 1 0-5.997.125 4 4 0 0 0-2.526 5.77 4 4 0 0 0 .556 6.588A4 4 0 1 0 12 18Z"/><path d="M12 5a3 3 0 1 1 5.997.125 4 4 0 0 1 2.526 5.77 4 4 0 0 1-.556 6.588A4 4 0 1 1 12 18Z"/><path d="M15 13a4.5 4.5 0 0 1-3-4 4.5 4.5 0 0 1-3 4"/><path d="M17.599 6.5a3 3 0 0 0 .399-1.375"/></svg>
                        </div>
                        <h3 class="text-4xl font-black text-slate-800 mb-4 tracking-tighter">Number Guess</h3>
                        <p class="text-xl font-bold text-indigo-600 mb-10 min-h-[1.5em]">${state.gameState.message}</p>
                        ${!state.gameState.isOver ? `
                            <div class="flex flex-col items-center space-y-6">
                                <input type="number" id="game-input" autofocus placeholder="?" class="w-40 text-5xl font-black text-center py-6 rounded-3xl bg-slate-50 border-2 border-slate-100 outline-none focus:border-indigo-300 focus:bg-white transition-all">
                                <button onclick="makeGuess()" class="w-full max-w-xs py-5 bg-indigo-600 text-white rounded-2xl font-black text-xl hover:bg-indigo-700 shadow-xl shadow-indigo-100 active:scale-95 transition-all">정답 확인</button>
                            </div>
                        ` : `
                            <button onclick="router.go('game')" class="w-full max-w-xs py-5 bg-emerald-500 text-white rounded-2xl font-black text-xl hover:bg-emerald-600 shadow-xl shadow-emerald-100 active:scale-95 transition-all">한 번 더 하기!</button>
                        `}
                    </div>
                </div>`;
            if(!state.gameState.isOver) setTimeout(() => document.getElementById('game-input').focus(), 100);
        }

        window.selectAns = (qIdx, oIdx) => { 
            if(!state.showResults) { 
                state.quizAnswers[qIdx] = oIdx; 
                router.render(); 
            } 
        };
        
        window.submitQuiz = (isAllAnswered) => { 
            if (state.showResults) {
                state.showResults = false;
                state.quizAnswers = {};
                router.render();
            } else if (isAllAnswered) {
                state.showResults = true; 
                router.render();
            }
        };

        window.openLoginModal = () => { 
            if(state.isDevMode) { 
                state.isDevMode = false; 
                updateDevUI(); 
            } else { 
                document.getElementById('login-modal').classList.remove('hidden'); 
                document.getElementById('dev-password').value = '';
                document.getElementById('login-error').classList.add('hidden');
                setTimeout(() => document.getElementById('dev-password').focus(), 100);
            } 
        };
        
        window.closeLoginModal = () => document.getElementById('login-modal').classList.add('hidden');
        
        window.attemptLogin = () => { 
            const pw = document.getElementById('dev-password').value;
            if(pw === '1234') { 
                state.isDevMode = true; 
                updateDevUI(); 
                closeLoginModal(); 
            } else { 
                document.getElementById('login-error').classList.remove('hidden'); 
            } 
        };

        // 엔터키 입력 시 로그인 처리
        document.getElementById('dev-password').addEventListener('keydown', (e) => {
            if (e.key === 'Enter') attemptLogin();
        });

        function updateDevUI() {
            document.getElementById('dev-badge').classList.toggle('hidden', !state.isDevMode);
            document.getElementById('dev-btn-text').innerText = state.isDevMode ? "로그아웃" : "개발자 로그인";
            
            const iconHTML = state.isDevMode 
                ? '<svg class="w-5 h-5 group-hover:-translate-x-1 transition-transform" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"/><polyline points="16 17 21 12 16 7"/><line x1="21" x2="9" y1="12" y2="12"/></svg>'
                : '<svg class="w-5 h-5 group-hover:rotate-12 transition-transform" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="16 18 22 12 16 6"/><polyline points="8 6 2 12 8 18"/></svg>';
            document.getElementById('dev-icon-container').innerHTML = iconHTML;
        }

        function getNavClass(isActive) {
            return `w-full flex items-center space-x-4 px-6 py-5 rounded-2xl font-black transition-all ${isActive ? 'bg-orange-500 text-white shadow-lg shadow-orange-100' : 'text-slate-400 hover:bg-slate-50'}`;
        }

        function updateSidebar() {
            const isDash = ['dashboard', 'unit-list', 'lesson'].includes(state.currentView);
            const isGame = state.currentView === 'game';
            const isDown = state.currentView === 'download';
            
            const navDash = document.getElementById('nav-dashboard');
            const navGame = document.getElementById('nav-game');
            const navDown = document.getElementById('nav-download');
            
            if (navDash) navDash.className = getNavClass(isDash);
            if (navGame) navGame.className = getNavClass(isGame);
            if (navDown) navDown.className = getNavClass(isDown);
        }

        // 초기 구동
        window.onload = () => router.render();
    </script>
</body>
</html>
