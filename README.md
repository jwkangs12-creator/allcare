# allcare
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>올케어 클래스 - 통합 QnA 플랫폼</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16.8/dist/katex.min.css">
    <script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.8/dist/katex.min.js"></script>
    <style>
        .drag-over { border-color: #3b82f6; background-color: #eff6ff; }
        .nav-active { color: #2563eb; border-bottom: 2px solid #2563eb; font-weight: bold; }
        .subject-active { background-color: #eff6ff; color: #1d4ed8; font-weight: bold; }
    </style>
</head>
<body class="bg-gray-50 text-gray-800 font-sans">

    <div id="auth-container" class="fixed inset-0 bg-gray-100 flex items-center justify-center z-50 p-4">
        <div class="bg-white p-6 md:p-8 rounded-2xl shadow-xl border border-gray-200 w-full max-w-md">
            <div class="text-center mb-6">
                <div class="inline-block bg-blue-600 text-white px-3 py-1.5 rounded-lg font-bold text-xl tracking-wider mb-2">All-Care</div>
                <h1 class="text-2xl font-bold text-gray-900" id="auth-title">올케어 클래스 로그인</h1>
                <p class="text-gray-500 text-xs mt-1">인증된 계정으로 나만의 질의응답 피드를 관리하세요.</p>
                <div class="mt-2 text-[11px] bg-blue-50 text-blue-700 p-2 rounded-lg text-left">
                    <strong>💡 관리자 테스트 계정</strong><br>
                    이메일: <code class="bg-white px-1 rounded">teacher@school.es.kr</code> / 비번: <code class="bg-white px-1 rounded">1234</code>
                </div>
            </div>

            <form id="auth-form" onsubmit="handleAuth(event)" class="space-y-4">
                <div id="name-field" class="hidden">
                    <label class="block text-xs font-semibold text-gray-600 mb-1">이름</label>
                    <input type="text" id="auth-name" placeholder="홍길동" class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-blue-500">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-gray-600 mb-1">이메일 계정</label>
                    <input type="email" id="auth-email" required placeholder="user@school.es.kr" class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-blue-500">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-gray-600 mb-1">비밀번호</label>
                    <input type="password" id="auth-pw" required placeholder="••••••••" class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-blue-500">
                </div>

                <button type="submit" id="auth-submit-btn" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-2.5 rounded-xl transition text-sm shadow-md">
                    로그인
                </button>
            </form>

            <div class="mt-4 text-center text-xs text-gray-500">
                <span id="auth-toggle-msg">아직 계정이 없으신가요?</span>
                <button onclick="toggleAuthMode()" id="auth-toggle-btn" class="text-blue-600 font-semibold underline ml-1">회원가입하기</button>
            </div>
        </div>
    </div>

    <div id="app-container" class="hidden">
        <header class="bg-white border-b border-gray-200 sticky top-0 z-40">
            <div class="max-w-7xl mx-auto px-4 h-16 flex items-center justify-between">
                <div class="flex items-center space-x-2 cursor-pointer" onclick="showPage('dashboard')">
                    <div class="bg-blue-600 text-white p-2 rounded-lg font-bold text-lg tracking-wider">All-Care</div>
                    <span class="font-bold text-xl text-gray-900 hidden sm:inline">올케어 클래스</span>
                </div>
                <nav class="flex space-x-6 font-medium h-full items-center">
                    <button id="nav-dashboard" onclick="showPage('dashboard')" class="nav-active py-5 text-sm md:text-base">전체 대시보드</button>
                    <button id="nav-ask" onclick="showPage('ask')" class="text-gray-500 hover:text-gray-900 py-5 text-sm md:text-base">질문하기</button>
                </nav>
                <div class="flex items-center space-x-3">
                    <div class="text-right hidden sm:block">
                        <p class="text-sm font-semibold text-gray-900" id="user-display-name">사용자</p>
                        <p class="text-xs text-gray-500" id="user-display-email">user@school.es.kr</p>
                    </div>
                    <button onclick="handleLogout()" class="text-xs bg-gray-100 hover:bg-gray-200 text-gray-600 px-2.5 py-1.5 rounded-lg font-medium transition">
                        로그아웃
                    </button>
                </div>
            </div>
        </header>

        <main id="page-dashboard" class="max-w-7xl mx-auto px-4 py-6 grid grid-cols-1 lg:grid-cols-4 gap-6">
            <aside class="lg:col-span-1 bg-white p-4 rounded-xl border border-gray-200 h-fit space-y-4">
                <div>
                    <h3 class="font-bold text-gray-900 text-sm mb-3"><i class="fa-solid fa-book mr-1"></i> 교과 선택</h3>
                    <div class="space-y-1.5 text-sm" id="subject-list">
                        <button onclick="setSubject('수학')" id="sub-수학" class="w-full text-left p-3 rounded-lg subject-active transition flex items-center justify-between">
                            <span>📐 수학 QnA 피드</span>
                            <span class="text-xs bg-blue-200 px-2 py-0.5 rounded-full" id="count-수학">0</span>
                        </button>
                        <button onclick="setSubject('국어')" id="sub-국어" class="w-full text-left p-3 rounded-lg text-gray-600 hover:bg-gray-50 transition flex items-center justify-between">
                            <span>📖 국어 QnA 피드</span>
                            <span class="text-xs bg-gray-200 px-2 py-0.5 rounded-full" id="count-국어">0</span>
                        </button>
                        <button onclick="setSubject('영어')" id="sub-영어" class="w-full text-left p-3 rounded-lg text-gray-600 hover:bg-gray-50 transition flex items-center justify-between">
                            <span>🔤 영어 QnA 피드</span>
                            <span class="text-xs bg-gray-200 px-2 py-0.5 rounded-full" id="count-영어">0</span>
                        </button>
                        <button onclick="setSubject('과학탐구')" id="sub-과학탐구" class="w-full text-left p-3 rounded-lg text-gray-600 hover:bg-gray-50 transition flex items-center justify-between">
                            <span>🧪 과학탐구 QnA 피드</span>
                            <span class="text-xs bg-gray-200 px-2 py-0.5 rounded-full" id="count-과학탐구">0</span>
                        </button>
                    </div>
                </div>
                <hr class="border-gray-100">
                <div class="text-center">
                    <span class="text-[11px] text-gray-400" id="role-status-text"><i class="fa-solid fa-lock mr-1"></i> 일반 학생 권한 작동 중</span>
                </div>
            </aside>

            <section class="lg:col-span-3 bg-white p-5 md:p-6 rounded-xl border border-gray-200 min-h-[450px] flex flex-col justify-between">
                <div>
                    <div class="flex justify-between items-center border-b border-gray-100 pb-3 mb-4">
                        <h2 class="text-lg font-bold text-gray-900">
                            <span id="target-subject-title" class="text-blue-600">수학</span> 질의응답 피드 리스트
                        </h2>
                        <span class="text-xs text-gray-500">모든 질문과 답변 보기</span>
                    </div>

                    <div class="space-y-4" id="dashboard-qna-container"></div>
                </div>

                <div class="mt-6 pt-4 border-t border-gray-100">
                    <button onclick="showPage('ask')" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-semibold py-3 rounded-xl transition flex items-center justify-center space-x-2">
                        <i class="fa-solid fa-pen-fancy"></i>
                        <span>새로운 질문 등록하기</span>
                    </button>
                </div>
            </section>
        </main>

        <main id="page-ask" class="max-w-2xl mx-auto px-4 py-8 hidden">
            <div class="bg-white rounded-2xl p-6 border border-gray-200 space-y-5 shadow-xs">
                <div class="border-b border-gray-100 pb-3 flex justify-between items-center">
                    <h2 class="text-lg font-bold text-gray-900" id="ask-page-title"><i class="fa-solid fa-circle-question text-blue-600 mr-1"></i> 질문 작성하기</h2>
                    <button onclick="cancelAsk()" class="text-xs text-gray-400 hover:text-gray-600">취소하고 돌아가기</button>
                </div>

                <div>
                    <label class="block text-xs font-semibold text-gray-700 mb-1">질문 대상 교과</label>
                    <select id="ask-subject" class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-blue-500">
                        <option value="수학">📐 수학</option>
                        <option value="국어">📖 국어</option>
                        <option value="영어">🔤 영어</option>
                        <option value="과학탐구">🧪 과학탐구</option>
                    </select>
                </div>

                <div>
                    <label class="block text-xs font-semibold text-gray-700 mb-1">질문 제목</label>
                    <input type="text" id="ask-title" placeholder="제목을 입력하세요" class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-blue-500">
                </div>

                <div>
                    <label class="block text-xs font-semibold text-gray-700 mb-1">📸 이미지 첨부 (Drag & Drop)</label>
                    <div id="drop-zone" class="border-2 border-dashed border-gray-300 rounded-xl p-5 text-center cursor-pointer hover:bg-gray-50 transition text-xs text-gray-500">
                        <i class="fa-solid fa-cloud-arrow-up text-2xl text-gray-400 mb-1"></i>
                        <p id="drop-zone-text" class="font-medium">이미지 파일을 여기에 끌어다 놓거나 클릭하여 선택하세요.</p>
                        <input type="file" id="file-input" class="hidden" accept="image/*">
                    </div>
                    
                    <div id="image-preview-container" class="hidden mt-2 relative border border-gray-200 rounded-xl overflow-hidden max-w-xs">
                        <img id="upload-img-preview" src="" class="w-full h-auto object-cover">
                        <button onclick="removeSelectedImage()" class="absolute top-1 right-1 bg-red-500 text-white rounded-full w-5 h-5 flex items-center justify-center text-[10px]"><i class="fa-solid fa-x"></i></button>
                    </div>

                    <button type="button" onclick="toggleWebcam()" class="mt-1.5 w-full py-1.5 bg-gray-100 hover:bg-gray-200 text-gray-600 rounded-lg text-[11px] font-semibold flex items-center justify-center space-x-1">
                        <i class="fa-solid fa-camera"></i>
                        <span>카메라 / 웹캠 렌더링 활성화</span>
                    </button>
                    <div id="camera-container" class="hidden mt-2 border rounded-lg overflow-hidden bg-black aspect-video relative">
                        <video id="webcam" class="w-full h-full" autoplay playsinline></video>
                        <button onclick="captureFromWebcam()" class="absolute bottom-2 left-1/2 -translate-x-1/2 bg-blue-600 text-white px-3 py-1 text-xs rounded-full shadow font-semibold">사진 촬영</button>
                    </div>
                </div>

                <div>
                    <div class="flex justify-between items-center mb-1">
                        <label class="block text-xs font-semibold text-gray-700">질문 내용 (LaTeX 수식 지원)</label>
                        <button onclick="insertFormulaSample()" class="text-[10px] text-blue-600 font-medium">[간이 수식 삽입]</button>
                    </div>
                    <textarea id="ask-content" rows="4" placeholder="수식은 기호 앞뒤에 $를 붙이세요. (예: $E=mc^2$)" class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-blue-500"></textarea>
                    
                    <div class="mt-2 p-3 bg-amber-50/60 border border-amber-200 rounded-lg text-xs">
                        <span class="font-semibold text-amber-800 block mb-1">💡 실시간 수식 프리뷰 결과창:</span>
                        <div id="math-preview" class="text-gray-800 min-h-[18px] whitespace-pre-wrap">내용을 입력하면 변환 결과가 출력됩니다.</div>
                    </div>
                </div>

                <div class="flex space-x-2 pt-2">
                    <button onclick="cancelAsk()" class="w-1/3 bg-gray-100 hover:bg-gray-200 text-gray-700 font-semibold py-2.5 rounded-xl text-sm">취소</button>
                    <button onclick="submitQuestion()" id="ask-submit-btn" class="w-2/3 bg-blue-600 hover:bg-blue-700 text-white font-semibold py-2.5 rounded-xl text-sm shadow-md">질문 등록 완료</button>
                </div>
            </div>
        </main>
    </div>

    <script>
        let isSignUpMode = false;
        let currentUser = null; 
        let activeSubject = '수학';
        let editingQuestionId = null;
        let attachedImageDataUrl = null; // 업로드된 이미지의 Base64 데이터를 담을 변수

        const userDatabase = {}; 
        // 전체 공유 및 관리형 관리를 위해 최상위 구조 통합 변환
        const qnaDatabase = {
            '수학': [
                { id: 1, author: '이민우', title: '개념 확인 기본 다지기 4번 수식 질문', content: '함수 $f(x) = \\int_{0}^{x} (t^2 - 1) dt$ 의 극대점이 생기는 구간 증명이 맞나요?', date: '2026-07-20', status: '답변 대기 중', image: null, replies: [] }
            ],
            '국어': [], '영어': [], '과학탐구': []
        };

        window.addEventListener('DOMContentLoaded', () => {
            // 목데이터 기본 유저 및 관리자 계정 생성
            userDatabase['student@school.es.kr'] = { name: '이민우', pw: '1234', role: 'student' };
            userDatabase['teacher@school.es.kr'] = { name: '관리자교사', pw: '1234', role: 'admin' }; // 관리자 계정

            document.getElementById('ask-content').addEventListener('input', updateMathPreview);
            initDragAndDrop();
        });

        function toggleAuthMode() {
            isSignUpMode = !isSignUpMode;
            const title = document.getElementById('auth-title');
            const nameField = document.getElementById('name-field');
            const submitBtn = document.getElementById('auth-submit-btn');
            const msg = document.getElementById('auth-toggle-msg');
            const toggleBtn = document.getElementById('auth-toggle-btn');

            if (isSignUpMode) {
                title.innerText = '올케어 클래스 회원가입';
                nameField.classList.remove('hidden');
                submitBtn.innerText = '가입하기';
                msg.innerText = '이미 계정이 있으신가요?';
                toggleBtn.innerText = '로그인하기';
            } else {
                title.innerText = '올케어 클래스 로그인';
                nameField.classList.add('hidden');
                submitBtn.innerText = '로그인';
                msg.innerText = '아직 계정이 없으신가요?';
                toggleBtn.innerText = '회원가입하기';
            }
        }

        function handleAuth(event) {
            event.preventDefault();
            const nameVal = document.getElementById('auth-name').value.trim();
            const emailVal = document.getElementById('auth-email').value.trim();
            const pwVal = document.getElementById('auth-pw').value;

            if (isSignUpMode) {
                if (!nameVal) { alert('이름을 입력해 주세요.'); return; }
                if (userDatabase[emailVal]) { alert('이미 존재하는 이메일 계정입니다.'); return; }
                
                userDatabase[emailVal] = { name: nameVal, pw: pwVal, role: 'student' };
                alert('회원가입이 완료되었습니다. 자동으로 로그인 상태로 연결됩니다.');
                currentUser = { email: emailVal, name: nameVal, role: 'student' };
            } else {
                const targetUser = userDatabase[emailVal];
                if (!targetUser || targetUser.pw !== pwVal) {
                    alert('이메일 또는 비밀번호가 일치하지 않습니다.');
                    return;
                }
                currentUser = { email: emailVal, name: targetUser.name, role: targetUser.role };
            }

            document.getElementById('auth-container').classList.add('hidden');
            document.getElementById('app-container').classList.remove('hidden');
            document.getElementById('user-display-name').innerText = `${currentUser.name} [${currentUser.role === 'admin' ? '관리자' : '학생'}]`;
            document.getElementById('user-display-email').innerText = currentUser.email;

            const roleStatusText = document.getElementById('role-status-text');
            if (currentUser.role === 'admin') {
                roleStatusText.innerHTML = `<i class="fa-solid fa-user-shield text-red-500 mr-1"></i> 마스터 관리자 권한 활성화`;
            } else {
                roleStatusText.innerHTML = `<i class="fa-solid fa-lock text-gray-400 mr-1"></i> 일반 학생 권한 작동 중`;
            }

            showPage('dashboard');
        }

        function handleLogout() {
            currentUser = null;
            document.getElementById('auth-email').value = '';
            document.getElementById('auth-pw').value = '';
            document.getElementById('auth-name').value = '';
            document.getElementById('app-container').classList.add('hidden');
            document.getElementById('auth-container').classList.remove('hidden');
            if(streamRef) closeCamera();
        }

        function showPage(pageId) {
            const pageDashboard = document.getElementById('page-dashboard');
            const pageAsk = document.getElementById('page-ask');
            const navDashboard = document.getElementById('nav-dashboard');
            const navAsk = document.getElementById('nav-ask');

            if (pageId === 'dashboard') {
                pageDashboard.classList.remove('hidden');
                pageAsk.classList.add('hidden');
                navDashboard.className = "nav-active py-5 text-sm md:text-base";
                navAsk.className = "text-gray-500 hover:text-gray-900 py-5 text-sm md:text-base";
                
                updateCountsBadge();
                renderSubjectFeed();
            } else if (pageId === 'ask') {
                pageDashboard.classList.add('hidden');
                pageAsk.classList.remove('hidden');
                navDashboard.className = "text-gray-500 hover:text-gray-900 py-5 text-sm md:text-base";
                navAsk.className = "nav-active py-5 text-sm md:text-base";
                
                if (!editingQuestionId) {
                    document.getElementById('ask-page-title').innerHTML = `<i class="fa-solid fa-circle-question text-blue-600 mr-1"></i> 질문 작성하기`;
                    document.getElementById('ask-submit-btn').innerText = '질문 등록 완료';
                    document.getElementById('ask-subject').value = activeSubject;
                    document.getElementById('ask-subject').disabled = false;
                }
            }
        }

        function setSubject(subject) {
            activeSubject = subject;
            const subjects = ['수학', '국어', '영어', '과학탐구'];
            
            subjects.forEach(sub => {
                const btn = document.getElementById(`sub-${sub}`);
                if (sub === subject) {
                    btn.className = "w-full text-left p-3 rounded-lg subject-active transition flex items-center justify-between";
                } else {
                    btn.className = "w-full text-left p-3 rounded-lg text-gray-600 hover:bg-gray-50 transition flex items-center justify-between";
                }
            });

            document.getElementById('target-subject-title').innerText = subject;
            renderSubjectFeed();
        }

        function parseMath(content) {
            return content.replace(/\$([^$]+)\$/g, function(match, formula) {
                try { return katex.renderToString(formula, { throwOnError: false }); } catch (e) { return match; }
            });
        }

        function renderSubjectFeed() {
            const container = document.getElementById('dashboard-qna-container');
            if (!currentUser) return;

            const currentFeeds = qnaDatabase[activeSubject] || [];

            if (currentFeeds.length === 0) {
                container.innerHTML = `
                    <div class="text-center py-12 border-2 border-dashed border-gray-100 rounded-xl">
                        <i class="fa-regular fa-folder-open text-3xl text-gray-300 mb-2 block"></i>
                        <p class="text-xs text-gray-400 font-medium">등록된 질문 피드가 없습니다.</p>
                    </div>`;
                return;
            }

            container.innerHTML = '';
            currentFeeds.forEach(feed => {
                const item = document.createElement('div');
                item.className = "p-4 bg-gray-50 rounded-xl border border-gray-100 shadow-3xs hover:bg-gray-100/30 transition space-y-3 relative";
                
                let parsedContent = parseMath(feed.content);

                // 이미지 태그 렌더링 검증
                let imageHtml = '';
                if (feed.image) {
                    imageHtml = `
                    <div class="my-2 max-w-sm rounded-lg overflow-hidden border border-gray-200 bg-white">
                        <img src="${feed.image}" class="w-full h-auto cursor-zoom-in" onclick="window.open(this.src)">
                    </div>`;
                }

                // 권한 조건 분기 (자신이 쓴 질문이거나 또는 마스터 관리자(admin) 계정일 때만 제어 버튼 노출)
                let questionAuthButtons = '';
                if (feed.author === currentUser.name || currentUser.role === 'admin') {
                    questionAuthButtons = `
                        <div class="flex space-x-2 text-[11px]">
                            <button onclick="editQuestion(${feed.id})" class="text-blue-600 hover:underline font-semibold"><i class="fa-solid fa-pen"></i> 수정</button>
                            <button onclick="deleteQuestion(${feed.id})" class="text-red-500 hover:underline font-semibold"><i class="fa-solid fa-trash"></i> 삭제</button>
                        </div>`;
                }

                let repliesHtml = '';
                if(feed.replies && feed.replies.length > 0) {
                    repliesHtml = `<div class="mt-3 pt-3 border-t border-gray-200/60 space-y-2">`;
                    feed.replies.forEach((reply, index) => {
                        let replyAuthButtons = '';
                        if (reply.author === currentUser.name || currentUser.role === 'admin') {
                            replyAuthButtons = `
                                <div class="flex space-x-2 text-[10px] ml-2">
                                    <button onclick="editReply(${feed.id}, ${index})" class="text-blue-600 hover:underline">수정</button>
                                    <button onclick="deleteReply(${feed.id}, ${index})" class="text-red-500 hover:underline">삭제</button>
                                </div>`;
                        }

                        repliesHtml += `
                            <div class="bg-white p-2.5 rounded-lg border border-gray-100 text-xs">
                                <div class="flex justify-between font-semibold text-gray-700 mb-1 items-center">
                                    <div class="flex items-center">
                                        <span>💬 ${reply.author}</span>
                                        ${replyAuthButtons}
                                    </div>
                                    <span class="text-[10px] text-gray-400 font-normal">${reply.date}</span>
                                </div>
                                <p class="text-gray-600 leading-relaxed whitespace-pre-wrap">${parseMath(reply.content)}</p>
                            </div>`;
                    });
                    repliesHtml += `</div>`;
                }

                item.innerHTML = `
                    <div class="flex justify-between items-start">
                        <h4 class="font-bold text-gray-900 text-sm flex-1">${feed.title}</h4>
                        <span class="text-[10px] ${feed.status === '답변 완료' ? 'bg-green-100 text-green-800' : 'bg-amber-100 text-amber-800'} font-semibold px-2 py-0.5 rounded ml-2 whitespace-nowrap">${feed.status}</span>
                    </div>
                    <div class="text-xs text-gray-600 leading-relaxed whitespace-pre-wrap">${parsedContent}</div>
                    
                    ${imageHtml}
                    
                    <div class="flex justify-between items-center text-[10px] text-gray-400 pt-1 border-t border-gray-100/60 pt-2">
                        <span>작성자: ${feed.author} | 등록일: ${feed.date}</span>
                        ${questionAuthButtons}
                    </div>
                    
                    ${repliesHtml}

                    <div class="mt-3 pt-2 border-t border-gray-100 flex gap-2">
                        <input type="text" id="reply-input-${feed.id}" placeholder="답변을 입력하세요 ($수식$ 가능)" class="flex-1 px-3 py-1.5 bg-white border border-gray-300 rounded-lg text-xs focus:outline-none focus:ring-1 focus:ring-blue-500">
                        <button onclick="submitReply(${feed.id})" id="reply-submit-btn-${feed.id}" class="bg-blue-600 hover:bg-blue-700 text-white px-3 py-1.5 rounded-lg text-xs font-semibold whitespace-nowrap transition">답변 달기</button>
                    </div>
                `;
                container.appendChild(item);
            });
        }

        function updateCountsBadge() {
            ['수학', '국어', '영어', '과학탐구'].forEach(sub => {
                const cnt = qnaDatabase[sub]?.length || 0;
                document.getElementById(`count-${sub}`).innerText = cnt;
            });
        }

        function submitQuestion() {
            const subject = document.getElementById('ask-subject').value;
            const title = document.getElementById('ask-title').value.trim();
            const content = document.getElementById('ask-content').value.trim();

            if (!title || !content) {
                alert('질문 제목과 세부 본문 내용을 정확히 기입하세요.');
                return;
            }

            if (editingQuestionId) {
                const feeds = qnaDatabase[activeSubject];
                const targetQuestion = feeds.find(f => f.id === editingQuestionId);
                if (targetQuestion) {
                    targetQuestion.title = title;
                    targetQuestion.content = content;
                    targetQuestion.image = attachedImageDataUrl; // 수정 시 이미지 교체 대응
                    alert('질문 수정이 완료되었습니다.');
                }
                editingQuestionId = null;
            } else {
                const today = new Date().toISOString().split('T')[0];
                qnaDatabase[subject].push({
                    id: Date.now(),
                    author: currentUser.name,
                    title: title,
                    content: content,
                    date: today,
                    status: '답변 대기 중',
                    image: attachedImageDataUrl, // 이미지 유무 저장
                    replies: []
                });
                alert('전체 공유 수강방에 질문 등록이 완료되었습니다.');
            }
            
            clearAskForm();
            setSubject(subject);
            showPage('dashboard');
        }

        function editQuestion(questionId) {
            const feeds = qnaDatabase[activeSubject];
            const targetQuestion = feeds.find(f => f.id === questionId);

            if (targetQuestion) {
                editingQuestionId = questionId;
                showPage('ask');

                document.getElementById('ask-page-title').innerHTML = `<i class="fa-solid fa-pen text-blue-600 mr-1"></i> 질문 수정하기`;
                document.getElementById('ask-submit-btn').innerText = '수정 완료';
                document.getElementById('ask-subject').value = activeSubject;
                document.getElementById('ask-subject').disabled = true; 
                document.getElementById('ask-title').value = targetQuestion.title;
                document.getElementById('ask-content').value = targetQuestion.content;
                
                if (targetQuestion.image) {
                    showImagePreview(targetQuestion.image);
                }
                updateMathPreview();
            }
        }

        function deleteQuestion(questionId) {
            if (confirm('정말로 이 질문을 삭제하시겠습니까? 관련 데이터가 소멸됩니다.')) {
                qnaDatabase[activeSubject] = qnaDatabase[activeSubject].filter(f => f.id !== questionId);
                updateCountsBadge();
                renderSubjectFeed();
            }
        }

        function cancelAsk() {
            editingQuestionId = null;
            clearAskForm();
            showPage('dashboard');
        }

        function clearAskForm() {
            document.getElementById('ask-title').value = '';
            document.getElementById('ask-content').value = '';
            document.getElementById('math-preview').innerHTML = '내용을 입력하면 변환 결과가 출력됩니다.';
            removeSelectedImage();
            if(streamRef) closeCamera();
        }

        function submitReply(questionId) {
            const inputElement = document.getElementById(`reply-input-${questionId}`);
            const replyContent = inputElement.value.trim();

            if(!replyContent) {
                alert('답변 내용을 입력해주세요.');
                return;
            }

            const feeds = qnaDatabase[activeSubject];
            const targetQuestion = feeds.find(f => f.id === questionId);

            if(targetQuestion) {
                const today = new Date().toISOString().split('T')[0];
                const editIndex = inputElement.getAttribute('data-edit-index');
                
                if (editIndex !== null) {
                    targetQuestion.replies[editIndex].content = replyContent;
                    inputElement.removeAttribute('data-edit-index');
                    document.getElementById(`reply-submit-btn-${questionId}`).innerText = '답변 달기';
                    alert('답변이 수정되었습니다.');
                } else {
                    targetQuestion.replies.push({
                        author: currentUser.name,
                        content: replyContent,
                        date: today
                    });
                    alert('답변이 등록되었습니다.');
                }

                targetQuestion.status = targetQuestion.replies.length > 0 ? '답변 완료' : '답변 대기 중';
                inputElement.value = '';
                renderSubjectFeed();
            }
        }

        function editReply(questionId, replyIndex) {
            const targetQuestion = qnaDatabase[activeSubject].find(f => f.id === questionId);
            if (targetQuestion && targetQuestion.replies[replyIndex]) {
                const reply = targetQuestion.replies[replyIndex];
                const inputElement = document.getElementById(`reply-input-${questionId}`);
                const submitBtn = document.getElementById(`reply-submit-btn-${questionId}`);
                
                inputElement.value = reply.content;
                inputElement.setAttribute('data-edit-index', replyIndex);
                inputElement.focus();
                submitBtn.innerText = '수정 완료';
            }
        }

        function deleteReply(questionId, replyIndex) {
            if (confirm('정말로 이 답변을 삭제하시겠습니까?')) {
                const targetQuestion = qnaDatabase[activeSubject].find(f => f.id === questionId);
                if (targetQuestion) {
                    targetQuestion.replies.splice(replyIndex, 1);
                    targetQuestion.status = targetQuestion.replies.length > 0 ? '답변 완료' : '답변 대기 중';
                    renderSubjectFeed();
                }
            }
        }

        function updateMathPreview() {
            const text = document.getElementById('ask-content').value;
            const preview = document.getElementById('math-preview');

            if (!text.trim()) { preview.innerHTML = '내용을 입력하면 변환 결과가 출력됩니다.'; return; }

            let rendered = parseMath(text);
            preview.innerHTML = rendered.replace(/\n/g, '<br>');
        }

        function insertFormulaSample() {
            const area = document.getElementById('ask-content');
            area.value += " 피타고라스 정리 기하 수식 $a^2 + b^2 = c^2$ ";
            updateMathPreview();
            area.focus();
        }

        // 이미지 로직 가공 모듈
        function processImageFile(file) {
            if (!file.type.startsWith('image/')) {
                alert('이미지 파일 파일 형식만 첨부 가능합니다.');
                return;
            }
            const reader = new FileReader();
            reader.onload = function(e) {
                showImagePreview(e.target.result);
            };
            reader.readAsDataURL(file);
        }

        function showImagePreview(dataUrl) {
            attachedImageDataUrl = dataUrl;
            document.getElementById('drop-zone-text').innerText = "이미지가 정상적으로 인식되었습니다.";
            const previewContainer = document.getElementById('image-preview-container');
            const previewImg = document.getElementById('upload-img-preview');
            previewImg.src = dataUrl;
            previewContainer.classList.remove('hidden');
        }

        function removeSelectedImage() {
            attachedImageDataUrl = null;
            document.getElementById('drop-zone-text').innerText = "이미지 파일을 여기에 끌어다 놓거나 클릭하여 선택하세요.";
            document.getElementById('image-preview-container').classList.add('hidden');
            document.getElementById('upload-img-preview').src = "";
            document.getElementById('file-input').value = "";
        }

        function initDragAndDrop() {
            const dropZone = document.getElementById('drop-zone');
            const fileInput = document.getElementById('file-input');
            
            dropZone.addEventListener('click', () => fileInput.click());
            dropZone.addEventListener('dragover', (e) => { e.preventDefault(); dropZone.classList.add('drag-over'); });
            dropZone.addEventListener('dragleave', () => dropZone.classList.remove('drag-over'));
            dropZone.addEventListener('drop', (e) => {
                e.preventDefault(); dropZone.classList.remove('drag-over');
                if (e.dataTransfer.files.length) {
                    processImageFile(e.dataTransfer.files[0]);
                }
            });
            fileInput.addEventListener('change', function() {
                if(this.files.length) {
                    processImageFile(this.files[0]);
                }
            });
        }

        let streamRef = null;
        async function toggleWebcam() {
            const container = document.getElementById('camera-container');
            const video = document.getElementById('webcam');
            
            if (container.classList.contains('hidden')) {
                container.classList.remove('hidden');
                try {
                    streamRef = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" } });
                    video.srcObject = streamRef;
                } catch (err) {
                    alert('사용 가능한 카메라 디바이스가 없거나 권한이 차단되었습니다.');
                    container.classList.add('hidden');
                }
            } else {
                closeCamera();
            }
        }

        function captureFromWebcam() {
            const video = document.getElementById('webcam');
            if (!streamRef) return;
            
            const canvas = document.createElement('canvas');
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            const ctx = canvas.getContext('2d');
            ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
            
            const dataUrl = canvas.toDataURL('image/png');
            showImagePreview(dataUrl);
            closeCamera();
        }

        function closeCamera() {
            document.getElementById('camera-container').classList.add('hidden');
            if (streamRef) {
                streamRef.getTracks().forEach(track => track.stop());
                streamRef = null;
            }
        }
    </script>
</body>
</html>
