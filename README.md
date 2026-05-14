html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>말미잘 자포 방출 정밀 시각화</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;700;900&display=swap');
        
        body {
            font-family: 'Noto Sans KR', sans-serif;
            background-color: #f1f5f9;
        }

        /* 부드러운 애니메이션을 위한 트랜지션 */
        .fade-in {
            animation: fadeIn 1s ease-in forwards;
        }

        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }

        .membrane-layer {
            stroke: #64748b;
            stroke-width: 4;
            stroke-dasharray: 8 4;
        }

        .stage-card {
            transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .active-step {
            border-color: #2563eb;
            background-color: #eff6ff;
            box-shadow: 0 4px 20px -2px rgba(37, 99, 235, 0.2);
        }

        /* 관 전개 애니메이션 속도 늦춤 */
        .eversion-slow {
            stroke-dasharray: 1000;
            stroke-dashoffset: 1000;
            animation: drawLong 4s forwards ease-in-out;
        }

        @keyframes drawLong {
            to { stroke-dashoffset: 0; }
        }

        /* 이온 흐름 속도 조절 */
        .ion-slow-move {
            animation: ionMove 3s infinite linear;
        }

        @keyframes ionMove {
            from { transform: translateY(0); opacity: 1; }
            to { transform: translateY(80px); opacity: 0; }
        }
    </style>
</head>
<body class="min-h-screen p-4 md:p-8">
    <div class="max-w-6xl mx-auto">
        <header class="text-center mb-10">
            <h1 class="text-3xl md:text-4xl font-black text-slate-900 mb-2">자포세포 반응성 및 방출 메커니즘</h1>
            <p class="text-slate-500 font-medium tracking-tight text-sm md:text-base">화학적 조절과 기계적 자극의 통합적 메커니즘 시각화</p>
        </header>

        <div class="grid grid-cols-1 lg:grid-cols-12 gap-8">
            <!-- 시각화 캔버스 -->
            <div class="lg:col-span-8 space-y-6">
                <div class="relative bg-white rounded-[2rem] shadow-xl border border-slate-200 overflow-hidden aspect-[16/10] flex items-center justify-center">
                    <svg id="main-canvas" viewBox="0 0 800 500" class="w-full h-full">
                        <defs>
                            <!-- 세포막 질감 -->
                            <pattern id="membranePattern" x="0" y="0" width="20" height="20" patternUnits="userSpaceOnUse">
                                <circle cx="10" cy="5" r="3" fill="#cbd5e1"/>
                                <line x1="10" y1="8" x2="10" y2="15" stroke="#cbd5e1" stroke-width="1"/>
                            </pattern>
                            <!-- 캡슐 그래디언트 -->
                            <radialGradient id="capsuleFill" cx="50%" cy="50%" r="50%">
                                <stop offset="0%" stop-color="#f0f9ff"/>
                                <stop offset="100%" stop-color="#bae6fd"/>
                            </radialGradient>
                        </defs>

                        <!-- 배경 레이어 (세포 환경) -->
                        <rect x="0" y="0" width="800" height="200" fill="#f8fafc"/> <!-- 외부 -->
                        <rect x="0" y="230" width="800" height="270" fill="#ffffff"/> <!-- 내부 -->
                        
                        <!-- 세포막 -->
                        <rect x="0" y="200" width="800" height="30" fill="url(#membranePattern)" />
                        <line x1="0" y1="200" x2="800" y2="200" stroke="#94a3b8" stroke-width="2"/>
                        <line x1="0" y1="230" x2="800" y2="230" stroke="#94a3b8" stroke-width="2"/>

                        <!-- 가변 콘텐츠 영역 -->
                        <g id="content-layer"></g>
                    </svg>

                    <!-- 단계 안내 레이블 -->
                    <div id="step-label" class="absolute top-6 left-6 px-5 py-2 bg-blue-600 text-white rounded-2xl text-sm font-black shadow-lg">
                        단계 ㄱ
                    </div>
                </div>

                <!-- 텍스트 설명 카드 -->
                <div class="bg-white p-8 rounded-[2rem] shadow-sm border border-slate-200 min-h-[160px]">
                    <h2 id="step-title" class="text-2xl font-bold text-slate-800 mb-3 underline decoration-blue-200 decoration-8 underline-offset-1">제목</h2>
                    <p id="step-desc" class="text-slate-600 text-lg leading-relaxed"></p>
                </div>
            </div>

            <!-- 우측 리스트 및 컨트롤 -->
            <div class="lg:col-span-4 flex flex-col gap-4">
                <div id="nav-list" class="space-y-3">
                    <!-- JS에서 동적 생성됨 -->
                </div>

                <div class="mt-4 p-6 bg-slate-900 rounded-[2rem] text-white">
                    <h3 class="text-xs font-bold text-slate-400 uppercase tracking-wides
