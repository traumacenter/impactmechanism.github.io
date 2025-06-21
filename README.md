<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>외상 기전별 임상 예시 시각화</title>
    <style>
        * {
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Arial', sans-serif;
            margin: 0;
            padding: 10px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: #333;
            min-height: 100vh;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: white;
            border-radius: 15px;
            padding: 20px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            width: 100%;
        }
        
        .header {
            text-align: center;
            margin-bottom: 30px;
        }
        
        .header h1 {
            color: #2c3e50;
            font-size: clamp(1.8em, 4vw, 2.5em);
            margin-bottom: 10px;
            line-height: 1.2;
        }
        
        .header p {
            color: #7f8c8d;
            font-size: clamp(1em, 2.5vw, 1.2em);
            line-height: 1.4;
            margin: 0 10px;
        }
        
        .mechanism-tabs {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 10px;
            margin-bottom: 25px;
        }
        
        .tab-btn {
            padding: 15px 10px;
            border: none;
            border-radius: 10px;
            font-size: clamp(11px, 2.5vw, 14px);
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
            text-align: center;
            line-height: 1.3;
            min-height: 60px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }
        
        @media (max-width: 768px) {
            .mechanism-tabs {
                grid-template-columns: 1fr;
                gap: 8px;
            }
            
            .tab-btn {
                font-size: 13px;
                padding: 12px 8px;
                min-height: 55px;
            }
        }
        
        @media (max-width: 480px) {
            body {
                padding: 5px;
            }
            
            .container {
                padding: 15px;
                border-radius: 10px;
            }
            
            .tab-btn {
                font-size: 12px;
                min-height: 50px;
            }
        }
        
        .tab-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(0,0,0,0.15);
        }
        
        @media (hover: none) {
            .tab-btn:hover {
                transform: none;
                box-shadow: 0 4px 15px rgba(0,0,0,0.1);
            }
        }
        
        .tab-direct { background: #e74c3c; color: white; }
        .tab-indirect { background: #f39c12; color: white; }
        .tab-rotational { background: #9b59b6; color: white; }
        .tab-deceleration { background: #3498db; color: white; }
        .tab-compression { background: #1abc9c; color: white; }
        .tab-active { background: #27ae60; color: white; }
        
        .example-selector {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 12px;
            margin-bottom: 25px;
        }
        
        @media (max-width: 768px) {
            .example-selector {
                grid-template-columns: 1fr;
                gap: 8px;
            }
        }
        
        .example-btn {
            padding: 12px 15px;
            border: 2px solid #ecf0f1;
            border-radius: 8px;
            background: white;
            cursor: pointer;
            transition: all 0.3s ease;
            text-align: left;
            font-size: clamp(12px, 2.5vw, 14px);
            line-height: 1.4;
        }
        
        .example-btn:hover {
            border-color: #3498db;
            background: #f8f9fa;
        }
        
        .example-btn.active {
            border-color: #27ae60;
            background: #d5f4e6;
        }
        
        .example-title {
            font-weight: bold;
            color: #2c3e50;
            margin-bottom: 5px;
        }
        
        .example-desc {
            color: #7f8c8d;
            font-size: clamp(11px, 2.2vw, 12px);
        }
        
        .simulation-area {
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 400px;
            height: 50vh;
            max-height: 500px;
            background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
            border-radius: 10px;
            margin-bottom: 25px;
            position: relative;
            overflow: hidden;
            border: 2px solid #dee2e6;
        }
        
        @media (max-width: 768px) {
            .simulation-area {
                min-height: 300px;
                height: 40vh;
                margin-bottom: 20px;
            }
        }
        
        @media (max-width: 480px) {
            .simulation-area {
                min-height: 250px;
                height: 35vh;
            }
        }
        
        .simulation-content {
            position: relative;
            width: 100%;
            height: 100%;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        
        .impact-object {
            position: absolute;
            transition: all 1.5s ease-in-out;
            z-index: 10;
        }
        
        .human-figure {
            width: clamp(60px, 12vw, 80px);
            height: clamp(120px, 24vw, 160px);
            position: relative;
            z-index: 5;
        }
        
        .head {
            width: clamp(20px, 4vw, 25px);
            height: clamp(20px, 4vw, 25px);
            background: #f4d03f;
            border-radius: 50%;
            margin: 0 auto 5px;
            position: relative;
        }
        
        .torso {
            width: clamp(30px, 6vw, 40px);
            height: clamp(50px, 10vw, 70px);
            background: #e74c3c;
            margin: 0 auto 5px;
            border-radius: 5px;
            position: relative;
        }
        
        .arms {
            position: absolute;
            top: 10px;
            left: -15px;
            right: -15px;
            height: 3px;
            background: #3498db;
            border-radius: 2px;
        }
        
        .legs {
            width: clamp(25px, 5vw, 35px);
            height: clamp(40px, 8vw, 55px);
            background: #3498db;
            margin: 0 auto;
            border-radius: 5px;
        }
        
        .injury-indicator {
            position: absolute;
            width: clamp(15px, 3vw, 20px);
            height: clamp(15px, 3vw, 20px);
            background: radial-gradient(circle, rgba(231,76,60,0.9) 0%, rgba(231,76,60,0.3) 100%);
            border-radius: 50%;
            opacity: 0;
            animation: pulse 1.5s infinite;
            z-index: 15;
        }
        
        @keyframes pulse {
            0%, 100% { transform: scale(1); opacity: 0.7; }
            50% { transform: scale(1.3); opacity: 1; }
        }
        
        .energy-path {
            position: absolute;
            opacity: 0;
            transition: opacity 0.5s ease;
            z-index: 8;
        }
        
        .explanation-panel {
            background: #f8f9fa;
            padding: clamp(15px, 4vw, 25px);
            border-radius: 10px;
            border-left: 5px solid #3498db;
        }
        
        .explanation-panel h3 {
            color: #2c3e50;
            margin-top: 0;
            font-size: clamp(1.2em, 3.5vw, 1.5em);
            line-height: 1.3;
        }
        
        .explanation-panel p {
            font-size: clamp(0.9em, 2.5vw, 1em);
            line-height: 1.5;
            margin: 10px 0;
        }
        
        .clinical-details {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 15px;
            margin-top: 20px;
        }
        
        @media (max-width: 768px) {
            .clinical-details {
                grid-template-columns: 1fr;
                gap: 12px;
                margin-top: 15px;
            }
        }
        
        .detail-card {
            background: white;
            padding: clamp(12px, 3vw, 15px);
            border-radius: 8px;
            border-left: 4px solid #e74c3c;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        
        .detail-card h4 {
            margin: 0 0 8px 0;
            color: #2c3e50;
            font-size: clamp(0.9em, 2.5vw, 1em);
            line-height: 1.3;
        }
        
        .detail-card p {
            font-size: clamp(0.8em, 2.2vw, 0.9em);
            line-height: 1.4;
            margin: 5px 0;
            color: #555;
        }
        
        .severity-tag {
            display: inline-block;
            padding: 3px 8px;
            border-radius: 12px;
            font-size: clamp(0.7em, 2vw, 0.8em);
            font-weight: bold;
            margin-bottom: 5px;
        }
        
        .severity-mild { background: #2ecc71; color: white; }
        .severity-moderate { background: #f39c12; color: white; }
        .severity-severe { background: #e74c3c; color: white; }
        .severity-critical { background: #8e44ad; color: white; }
        
        .hidden {
            display: none;
        }
        
        @keyframes impact {
            0% { transform: scale(1); }
            25% { transform: scale(1.2); }
            50% { transform: scale(0.9); }
            100% { transform: scale(1); }
        }
        
        @keyframes energy-flow {
            0% { stroke-dasharray: 0, 100; }
            100% { stroke-dasharray: 100, 0; }
        }
        
        .impact-animation {
            animation: impact 0.6s ease-out;
        }
        
        @media (max-width: 768px) and (orientation: landscape) {
            .simulation-area {
                height: 60vh;
                min-height: 300px;
            }
        }
        
        @media (max-width: 320px) {
            .header h1 {
                font-size: 1.5em;
            }
            
            .tab-btn {
                font-size: 11px;
                padding: 10px 6px;
                min-height: 45px;
            }
            
            .simulation-area {
                min-height: 200px;
                height: 30vh;
            }
            
            .explanation-panel {
                padding: 12px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>⚡ 외상 기전별 임상 예시</h1>
            <p>5가지 주요 외상 기전과 실제 임상 사례를 시각적으로 학습해보세요</p>
        </div>
        
        <div class="mechanism-tabs">
            <button class="tab-btn tab-direct" onclick="selectMechanism('direct')">
                <span>🎯 직접 충격</span>
                <small>Direct Impact</small>
            </button>
            <button class="tab-btn tab-indirect" onclick="selectMechanism('indirect')">
                <span>↗️ 간접 충격</span>
                <small>Indirect Impact</small>
            </button>
            <button class="tab-btn tab-rotational" onclick="selectMechanism('rotational')">
                <span>🌀 회전 손상</span>
                <small>Rotational Injury</small>
            </button>
            <button class="tab-btn tab-deceleration" onclick="selectMechanism('deceleration')">
                <span>⚡ 감속 손상</span>
                <small>Deceleration Injury</small>
            </button>
            <button class="tab-btn tab-compression" onclick="selectMechanism('compression')">
                <span>🔄 압축 손상</span>
                <small>Compression Injury</small>
            </button>
        </div>
        
        <div class="example-selector" id="example-selector">
            <!-- 예시 버튼들이 동적으로 생성됩니다 -->
        </div>
        
        <div class="simulation-area">
            <div class="simulation-content" id="simulation-content">
                <div class="human-figure">
                    <div class="head"></div>
                    <div class="torso">
                        <div class="arms"></div>
                    </div>
                    <div class="legs"></div>
                </div>
                
                <!-- 손상 지시자들 -->
                <div class="injury-indicator" id="head-injury"></div>
                <div class="injury-indicator" id="chest-injury"></div>
                <div class="injury-indicator" id="arm-injury"></div>
                <div class="injury-indicator" id="leg-injury"></div>
                <div class="injury-indicator" id="spine-injury"></div>
                
                <!-- 충격 객체 (동적으로 생성) -->
                <div class="impact-object" id="impact-object"></div>
                
                <!-- 에너지 전달 경로 -->
                <svg class="energy-path" id="energy-path" width="100%" height="100%">
                    <path id="energy-line" stroke="#e74c3c" stroke-width="3" stroke-dasharray="5,5" fill="none"/>
                </svg>
            </div>
        </div>
        
        <div class="explanation-panel" id="explanation-panel">
            <h3>🔍 외상 기전을 선택해주세요</h3>
            <p>위의 탭을 클릭하여 다양한 외상 기전별 임상 예시를 확인해보세요.</p>
        </div>
    </div>

    <script>
        const mechanismData = {
            direct: {
                title: "🎯 직접 충격 (Direct Impact)",
                description: "물체가 직접 신체에 충돌하여 발생하는 손상",
                examples: [
                    {
                        id: "baseball",
                        title: "야구공 충돌",
                        desc: "야구공이 머리에 직접 충돌",
                        content: {
                            scenario: "야구 경기 중 투수가 던진 공이 타자의 좌측 측두부에 직접 충돌 (시속 140km)",
                            mechanism: "고속 직접 충격으로 충돌 지점에서 최대 에너지 집중",
                            injuries: [
                                {
                                    title: "1차 손상 (직접 충격)",
                                    severity: "severe",
                                    details: "• 좌측 측두골 골절\n• 두피 열상 및 혈종\n• 뇌진탕 (Grade II)"
                                },
                                {
                                    title: "2차 손상 (뇌 내부)",
                                    severity: "moderate",
                                    details: "• 뇌좌상 (대측 충격)\n• 일시적 의식 소실\n• 기억 장애"
                                },
                                {
                                    title: "간호 중점사항",
                                    severity: "critical",
                                    details: "• 신경학적 관찰 (GCS)\n• 두개내압 상승 징후\n• 24시간 집중 모니터링"
                                }
                            ]
                        },
                        impactObject: "⚾",
                        impactPosition: { x: "30%", y: "20%" },
                        targetPosition: { x: "45%", y: "25%" },
                        injuryPositions: ["head-injury"]
                    },
                    {
                        id: "punch",
                        title: "주먹 충돌",
                        desc: "주먹이 안면부에 직접 충돌",
                        content: {
                            scenario: "격투기 경기 중 오른쪽 훅이 상대방의 좌측 안와부에 직접 충돌",
                            mechanism: "중속 직접 충격으로 뼈 구조가 약한 안와부에 집중 손상",
                            injuries: [
                                {
                                    title: "안면부 골절",
                                    severity: "moderate",
                                    details: "• 좌측 안와 하벽 골절\n• 광대뼈 골절\n• 안구 함몰"
                                },
                                {
                                    title: "연부조직 손상",
                                    severity: "mild",
                                    details: "• 안면부 부종 및 혈종\n• 결막하 출혈\n• 열상"
                                },
                                {
                                    title: "기능적 장애",
                                    severity: "moderate",
                                    details: "• 복시 (이중시야)\n• 안구 운동 장애\n• 안면 감각 저하"
                                }
                            ]
                        },
                        impactObject: "👊",
                        impactPosition: { x: "20%", y: "30%" },
                        targetPosition: { x: "40%", y: "25%" },
                        injuryPositions: ["head-injury"]
                    },
                    {
                        id: "wall",
                        title: "벽 충돌",
                        desc: "머리가 벽에 직접 부딪힘",
                        content: {
                            scenario: "음주 상태로 넘어지면서 후두부가 콘크리트 벽에 직접 충돌",
                            mechanism: "저속 직접 충격이지만 딱딱한 표면으로 인한 국소 집중 손상",
                            injuries: [
                                {
                                    title: "두피 손상",
                                    severity: "mild",
                                    details: "• 후두부 열상 (5cm)\n• 두피 혈종\n• 국소 부종"
                                },
                                {
                                    title: "뇌 손상",
                                    severity: "moderate",
                                    details: "• 뇌진탕 (Grade I)\n• 일시적 혼란 상태\n• 두통 및 어지럼증"
                                },
                                {
                                    title: "관찰 포인트",
                                    severity: "moderate",
                                    details: "• 지연성 두개내 출혈\n• 음주로 인한 증상 masking\n• 72시간 관찰 필요"
                                }
                            ]
                        },
                        impactObject: "🧱",
                        impactPosition: { x: "70%", y: "25%" },
                        targetPosition: { x: "55%", y: "20%" },
                        injuryPositions: ["head-injury"]
                    },
                    {
                        id: "knee",
                        title: "무릎 충돌",
                        desc: "무릎이 바닥에 직접 부딪힘",
                        content: {
                            scenario: "계단에서 미끄러져 무릎을 꿇으며 콘크리트 바닥에 무릎이 직접 충돌",
                            mechanism: "중간 속도 직접 충격으로 슬개골에 집중된 압축력 전달",
                            injuries: [
                                {
                                    title: "골격계 손상",
                                    severity: "severe",
                                    details: "• 슬개골 분쇄 골절\n• 무릎 관절면 손상\n• 연골 결손"
                                },
                                {
                                    title: "연부조직 손상",
                                    severity: "moderate",
                                    details: "• 무릎 전면 열상\n• 전십자인대 타박\n• 관절 내 출혈"
                                },
                                {
                                    title: "기능적 예후",
                                    severity: "moderate",
                                    details: "• 무릎 신전 장애\n• 보행 기능 저하\n• 장기적 관절염 위험"
                                }
                            ]
                        },
                        impactObject: "🏢",
                        impactPosition: { x: "50%", y: "80%" },
                        targetPosition: { x: "50%", y: "70%" },
                        injuryPositions: ["leg-injury"]
                    }
                ]
            },
            indirect: {
                title: "↗️ 간접 충격 (Indirect Impact)",
                description: "충격이 가해진 부위와 다른 곳에서 손상이 발생",
                examples: [
                    {
                        id: "ankle_fall",
                        title: "발목 낙상",
                        desc: "발목 충격이 상위로 전달",
                        content: {
                            scenario: "2m 높이에서 점프 후 오른발로 착지, 에너지가 상행 전달",
                            mechanism: "축성 에너지 전달로 약한 관절부터 순차적 손상 발생",
                            injuries: [
                                {
                                    title: "1차 손상 (충격점)",
                                    severity: "moderate",
                                    details: "• 우측 발목 염좌\n• 종골 미세 골절\n• 족관절 부종"
                                },
                                {
                                    title: "2차 손상 (에너지 전달)",
                                    severity: "severe",
                                    details: "• 경골/비골 골절\n• 무릎 반월판 파열\n• 고관절 타박"
                                },
                                {
                                    title: "3차 손상 (종점)",
                                    severity: "moderate",
                                    details: "• 요추 압박골절 (L1)\n• 척추 주변 근육 손상\n• 추간판 돌출"
                                }
                            ]
                        },
                        impactObject: "⬇️",
                        impactPosition: { x: "50%", y: "90%" },
                        targetPosition: { x: "50%", y: "75%" },
                        injuryPositions: ["leg-injury", "spine-injury"]
                    },
                    {
                        id: "wrist_fall",
                        title: "손목 낙상",
                        desc: "손목으로 지탱하며 에너지 전달",
                        content: {
                            scenario: "자전거에서 넘어지며 펼친 손으로 땅을 짚어 체중을 지탱",
                            mechanism: "방사성 에너지 전달로 상지 전체에 분산되어 손상 발생",
                            injuries: [
                                {
                                    title: "손목 관절 (1차)",
                                    severity: "severe",
                                    details: "• Colles 골절 (요골 원위부)\n• 수근골 탈구\n• 손목 관절면 손상"
                                },
                                {
                                    title: "팔꿈치 관절 (2차)",
                                    severity: "moderate",
                                    details: "• 요골두 아탈구\n• 내측 측부인대 손상\n• 관절 내 출혈"
                                },
                                {
                                    title: "어깨 관절 (3차)",
                                    severity: "mild",
                                    details: "• 견봉쇄골 관절 염좌\n• 회전근개 타박\n• 어깨 관절 불안정성"
                                }
                            ]
                        },
                        impactObject: "🚴",
                        impactPosition: { x: "30%", y: "40%" },
                        targetPosition: { x: "35%", y: "50%" },
                        injuryPositions: ["arm-injury"]
                    }
                ]
            },
            rotational: {
                title: "🌀 회전 손상 (Rotational Injury)",
                description: "회전력에 의해 나선형 골절과 인대 파열 발생",
                examples: [
                    {
                        id: "ski_fracture",
                        title: "스키 나선형 골절",
                        desc: "발이 고정된 상태에서 몸통 회전",
                        content: {
                            scenario: "스키 부츠가 눈에 박힌 상태에서 몸이 회전하며 넘어짐",
                            mechanism: "강력한 회전 토크로 인한 나선형 전단력이 뼈에 작용",
                            injuries: [
                                {
                                    title: "골격계 손상",
                                    severity: "severe",
                                    details: "• 경골 나선형 골절\n• 비골 동반 골절\n• 골절선이 나선형으로 진행"
                                },
                                {
                                    title: "연부조직 손상",
                                    severity: "moderate",
                                    details: "• 하퇴부 근육 파열\n• 혈관 손상 위험\n• 신경 견인 손상"
                                },
                                {
                                    title: "합병증 위험",
                                    severity: "critical",
                                    details: "• 구획증후군 발생 위험\n• 불유합 가능성\n• 장기간 재활 필요"
                                }
                            ]
                        },
                        impactObject: "🎿",
                        impactPosition: { x: "45%", y: "85%" },
                        targetPosition: { x: "50%", y: "65%" },
                        injuryPositions: ["leg-injury"]
                    },
                    {
                        id: "acl_tear",
                        title: "ACL 파열",
                        desc: "무릎 고정 상태에서 몸통 회전",
                        content: {
                            scenario: "축구 경기 중 발이 잔디에 고정된 상태에서 상체가 급격히 회전",
                            mechanism: "무릎 관절의 회전 불안정성으로 인한 인대 전단 파열",
                            injuries: [
                                {
                                    title: "주요 인대 손상",
                                    severity: "severe",
                                    details: "• 전십자인대 완전 파열\n• 내측 반월판 파열\n• 내측 측부인대 손상"
                                },
                                {
                                    title: "관절 손상",
                                    severity: "moderate",
                                    details: "• 관절 불안정성\n• 관절내 출혈\n• 연골 손상"
                                },
                                {
                                    title: "기능적 장애",
                                    severity: "severe",
                                    details: "• 무릎 giving way\n• 운동 능력 저하\n• 수술적 치료 필요"
                                }
                            ]
                        },
                        impactObject: "⚽",
                        impactPosition: { x: "40%", y: "80%" },
                        targetPosition: { x: "50%", y: "70%" },
                        injuryPositions: ["leg-injury"]
                    }
                ]
            },
            deceleration: {
                title: "⚡ 감속 손상 (Deceleration Injury)",
                description: "급작스러운 속도 변화로 인한 전단력 손상",
                examples: [
                    {
                        id: "aortic_tear",
                        title: "대동맥 파열",
                        desc: "고속 충돌 시 대동맥 전단 손상",
                        content: {
                            scenario: "시속 80km로 주행 중 정면 충돌, 급작스러운 감속으로 대동맥 손상",
                            mechanism: "심장과 대동맥의 서로 다른 감속으로 고정점에서 전단력 발생",
                            injuries: [
                                {
                                    title: "대동맥 손상",
                                    severity: "critical",
                                    details: "• 대동맥 isthmus 부분 파열\n• 종격동 혈종\n• 가성 동맥류 형성"
                                },
                                {
                                    title: "임상 증상",
                                    severity: "critical",
                                    details: "• 흉통 (찢어지는 양상)\n• 혈압 차이 (상하지)\n• 쉰 목소리"
                                },
                                {
                                    title: "응급 처치",
                                    severity: "critical",
                                    details: "• 즉시 수술적 치료\n• 혈압 조절\n• 항응고제 금기"
                                }
                            ]
                        },
                        impactObject: "🚗",
                        impactPosition: { x: "20%", y: "50%" },
                        targetPosition: { x: "50%", y: "50%" },
                        injuryPositions: ["chest-injury"]
                    },
                    {
                        id: "brain_injury",
                        title: "뇌 축삭 손상",
                        desc: "뇌조직의 밀도 차이로 인한 손상",
                        content: {
                            scenario: "교통사고로 인한 급격한 머리 움직임으로 뇌 조직 전단 손상",
                            mechanism: "회백질과 백질의 밀도 차이로 인한 미만성 축삭 손상",
                            injuries: [
                                {
                                    title: "신경학적 손상",
                                    severity: "critical",
                                    details: "• 미만성 축삭 손상\n• 의식 소실 (6시간 이상)\n• 코마 상태"
                                },
                                {
                                    title: "인지 기능 장애",
                                    severity: "severe",
                                    details: "• 기억 장애\n• 집중력 저하\n• 판단력 장애"
                                },
                                {
                                    title: "장기 예후",
                                    severity: "severe",
                                    details: "• 영구적 장애 가능\n• 장기간 재활 필요\n• 사회 복귀 어려움"
                                }
                            ]
                        },
                        impactObject: "⚡",
                        impactPosition: { x: "30%", y: "20%" },
                        targetPosition: { x: "50%", y: "25%" },
                        injuryPositions: ["head-injury"]
                    }
                ]
            },
            compression: {
                title: "🔄 압축 손상 (Compression Injury)",
                description: "압축력에 의한 골절과 내장기관 파열",
                examples: [
                    {
                        id: "spine_compression",
                        title: "척추 압박골절",
                        desc: "수직 압축력으로 인한 척추체 붕괴",
                        content: {
                            scenario: "3층 높이에서 추락하여 발부터 착지, 강력한 압축력이 척추에 전달",
                            mechanism: "축성 압축력이 척추체의 압축 강도를 초과하여 붕괴 발생",
                            injuries: [
                                {
                                    title: "척추체 손상",
                                    severity: "severe",
                                    details: "• L1 척추체 압박골절\n• 척추체 높이 50% 감소\n• 후방 인대 손상"
                                },
                                {
                                    title: "신경학적 손상",
                                    severity: "moderate",
                                    details: "• 척수 압박 (경미)\n• 하지 감각 저하\n• 근력 약화"
                                },
                                {
                                    title: "치료 및 예후",
                                    severity: "moderate",
                                    details: "• 척추 고정술 필요\n• 3개월 침상 안정\n• 만성 요통 위험"
                                }
                            ]
                        },
                        impactObject: "⬇️",
                        impactPosition: { x: "50%", y: "10%" },
                        targetPosition: { x: "50%", y: "45%" },
                        injuryPositions: ["spine-injury"]
                    },
                    {
                        id: "flail_chest",
                        title: "플레일 흉부",
                        desc: "다발성 늑골골절로 인한 흉벽 불안정",
                        content: {
                            scenario: "대형 트럭에 깔려 흉부에 강력한 압축력이 가해진 상태",
                            mechanism: "전후 압축으로 여러 늑골이 두 곳 이상에서 골절되어 흉벽 분절화",
                            injuries: [
                                {
                                    title: "흉벽 손상",
                                    severity: "critical",
                                    details: "• 우측 4-8번 늑골 골절\n• 흉벽 분절 (Flail segment)\n• 역설적 호흡 운동"
                                },
                                {
                                    title: "폐 손상",
                                    severity: "severe",
                                    details: "• 우측 폐 좌상\n• 기흉 및 혈흉\n• 호흡 부전"
                                },
                                {
                                    title: "치료 접근",
                                    severity: "critical",
                                    details: "• 즉시 기계 환기\n• 통증 조절\n• 수술적 고정 고려"
                                }
                            ]
                        },
                        impactObject: "🚛",
                        impactPosition: { x: "70%", y: "40%" },
                        targetPosition: { x: "50%", y: "40%" },
                        injuryPositions: ["chest-injury"]
                    }
                ]
            }
        };

        let currentMechanism = 'direct';
        let currentExample = 'baseball';

        function selectMechanism(mechanism) {
            currentMechanism = mechanism;
            
            // 탭 활성화
            document.querySelectorAll('.tab-btn').forEach(btn => {
                btn.classList.remove('tab-active');
            });
            document.querySelector(`.tab-${mechanism}`).classList.add('tab-active');
            
            // 예시 버튼 생성
            createExampleButtons();
            
            // 첫 번째 예시 자동 선택
            const firstExample = mechanismData[mechanism].examples[0];
            currentExample = firstExample.id;
            selectExample(firstExample.id);
        }

        function createExampleButtons() {
            const selector = document.getElementById('example-selector');
            const examples = mechanismData[currentMechanism].examples;
            
            selector.innerHTML = examples.map(example => `
                <div class="example-btn" onclick="selectExample('${example.id}')">
                    <div class="example-title">${example.title}</div>
                    <div class="example-desc">${example.desc}</div>
                </div>
            `).join('');
        }

        function selectExample(exampleId) {
            currentExample = exampleId;
            
            // 예시 버튼 활성화
            document.querySelectorAll('.example-btn').forEach(btn => {
                btn.classList.remove('active');
            });
            
            const exampleBtns = document.querySelectorAll('.example-btn');
            const examples = mechanismData[currentMechanism].examples;
            const exampleIndex = examples.findIndex(ex => ex.id === exampleId);
            if (exampleBtns[exampleIndex]) {
                exampleBtns[exampleIndex].classList.add('active');
            }
            
            // 시뮬레이션 실행
            runSimulation();
            
            // 설명 업데이트
            updateExplanation();
        }

        function runSimulation() {
            const example = mechanismData[currentMechanism].examples.find(ex => ex.id === currentExample);
            if (!example) return;
            
            // 기존 애니메이션 리셋
            resetSimulation();
            
            // 충격 객체 설정
            const impactObject = document.getElementById('impact-object');
            impactObject.textContent = example.impactObject;
            impactObject.style.fontSize = 'clamp(24px, 5vw, 32px)';
            impactObject.style.left = example.impactPosition.x;
            impactObject.style.top = example.impactPosition.y;
            impactObject.style.opacity = '1';
            
            // 충격 애니메이션
            setTimeout(() => {
                impactObject.style.left = example.targetPosition.x;
                impactObject.style.top = example.targetPosition.y;
                
                setTimeout(() => {
                    // 충격 효과
                    const humanFigure = document.querySelector('.human-figure');
                    humanFigure.classList.add('impact-animation');
                    
                    // 손상 지시자 표시
                    example.injuryPositions.forEach((injuryId, index) => {
                        setTimeout(() => {
                            const indicator = document.getElementById(injuryId);
                            indicator.style.opacity = '1';
                            
                            // 위치 조정
                            if (injuryId === 'head-injury') {
                                indicator.style.left = '48%';
                                indicator.style.top = '15%';
                            } else if (injuryId === 'chest-injury') {
                                indicator.style.left = '48%';
                                indicator.style.top = '35%';
                            } else if (injuryId === 'arm-injury') {
                                indicator.style.left = '35%';
                                indicator.style.top = '30%';
                            } else if (injuryId === 'leg-injury') {
                                indicator.style.left = '48%';
                                indicator.style.top = '65%';
                            } else if (injuryId === 'spine-injury') {
                                indicator.style.left = '52%';
                                indicator.style.top = '45%';
                            }
                        }, index * 300);
                    });
                    
                    // 애니메이션 클래스 제거
                    setTimeout(() => {
                        humanFigure.classList.remove('impact-animation');
                    }, 600);
                    
                }, 200);
            }, 500);
        }

        function resetSimulation() {
            // 손상 지시자 숨기기
            document.querySelectorAll('.injury-indicator').forEach(indicator => {
                indicator.style.opacity = '0';
            });
            
            // 충격 객체 리셋
            const impactObject = document.getElementById('impact-object');
            impactObject.style.opacity = '0';
        }

        function updateExplanation() {
            const mechanism = mechanismData[currentMechanism];
            const example = mechanism.examples.find(ex => ex.id === currentExample);
            
            if (!example) return;
            
            const panel = document.getElementById('explanation-panel');
            panel.innerHTML = `
                <h3>${mechanism.title}</h3>
                <p><strong>📋 시나리오:</strong> ${example.content.scenario}</p>
                <p><strong>⚙️ 손상 기전:</strong> ${example.content.mechanism}</p>
                <div class="clinical-details">
                    ${example.content.injuries.map(injury => `
                        <div class="detail-card">
                            <h4>${injury.title}</h4>
                            <span class="severity-tag severity-${injury.severity}">
                                ${injury.severity === 'mild' ? '경미' : 
                                  injury.severity === 'moderate' ? '중등도' : 
                                  injury.severity === 'severe' ? '심각' : '치명적'}
                            </span>
                            <p>${injury.details.replace(/\n/g, '<br>')}</p>
                        </div>
                    `).join('')}
                </div>
            `;
        }

        // 초기화
        window.addEventListener('load', () => {
            selectMechanism('direct');
        });
    </script>
</body>
</html>
