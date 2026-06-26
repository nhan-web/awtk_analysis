<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>AWTK 2026 Session Survey Dashboard</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
      background: #f0f4f8;
      color: #1a1a2e;
    }
    header {
      background: linear-gradient(135deg, #066AFE 0%, #032D60 100%);
      color: white;
      padding: 28px 40px 24px;
    }
    header .tag {
      font-size: 11px; font-weight: 700; letter-spacing: 0.12em;
      text-transform: uppercase; background: rgba(255,255,255,0.18);
      padding: 4px 10px; border-radius: 20px; display: inline-block; margin-bottom: 10px;
    }
    header h1 { font-size: 26px; font-weight: 700; margin-bottom: 4px; }
    header p  { font-size: 14px; opacity: 0.8; }

    .main { padding: 32px 40px; max-width: 1400px; margin: 0 auto; }

    .kpi-grid {
      display: grid; grid-template-columns: repeat(5, 1fr); gap: 16px; margin-bottom: 28px;
    }
    .kpi {
      background: white; border-radius: 14px; padding: 20px 22px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.06); border-top: 4px solid transparent;
    }
    .kpi.blue   { border-color: #066AFE; }
    .kpi.teal   { border-color: #0EA58A; }
    .kpi.pink   { border-color: #EE5FA7; }
    .kpi.yellow { border-color: #F5B800; }
    .kpi.purple { border-color: #7B5EA7; }
    .kpi .label { font-size: 11px; color: #6b7280; font-weight: 600; text-transform: uppercase; letter-spacing: 0.08em; margin-bottom: 8px; }
    .kpi .val   { font-size: 28px; font-weight: 800; color: #111827; line-height: 1; }
    .kpi .sub   { font-size: 12px; color: #6b7280; margin-top: 5px; }

    .section-title {
      font-size: 15px; font-weight: 700; color: #374151; margin-bottom: 14px;
      padding-left: 10px; border-left: 3px solid #066AFE;
    }
    .two-col   { display: grid; grid-template-columns: 1fr 1fr;       gap: 20px; margin-bottom: 28px; }
    .three-col { display: grid; grid-template-columns: 1fr 1fr 1fr;   gap: 20px; margin-bottom: 28px; }
    .one-col   { margin-bottom: 28px; }

    .card {
      background: white; border-radius: 14px; padding: 22px 24px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.06);
    }
    .card canvas { max-height: 280px; }

    table { width: 100%; border-collapse: collapse; font-size: 13px; }
    th {
      background: #f8fafc; text-align: left; padding: 10px 12px;
      font-size: 11px; font-weight: 700; color: #6b7280;
      text-transform: uppercase; letter-spacing: 0.06em; border-bottom: 2px solid #e5e7eb;
    }
    td { padding: 9px 12px; border-bottom: 1px solid #f3f4f6; color: #374151; }
    tr:last-child td { border-bottom: none; }
    tr:hover td { background: #f9fafb; }

    .badge {
      display: inline-block; padding: 2px 8px; border-radius: 20px;
      font-size: 11px; font-weight: 600;
    }
    .badge-blue   { background: #EBF3FF; color: #066AFE; }
    .badge-green  { background: #DCFCE7; color: #16a34a; }
    .badge-orange { background: #FEF3C7; color: #d97706; }
    .badge-pink   { background: #FDF2F8; color: #be185d; }
    .badge-purple { background: #F5F3FF; color: #7C3AED; }
    .badge-gray   { background: #F3F4F6; color: #6b7280; }
    .badge-red    { background: #FEF2F2; color: #dc2626; }

    .rate-bar-wrap { display: flex; align-items: center; gap: 8px; }
    .rate-bar { height: 6px; border-radius: 3px; background: #e5e7eb; flex: 1; min-width: 60px; }
    .rate-bar-fill { height: 100%; border-radius: 3px; }
    .good { color: #16a34a; font-weight: 700; }
    .warn { color: #d97706; font-weight: 700; }
    .bad  { color: #dc2626; font-weight: 700; }
    .na   { color: #9ca3af; }

    .insight-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px; margin-bottom: 28px; }
    .insight {
      background: white; border-radius: 14px; padding: 18px 20px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.06); border-left: 4px solid #066AFE;
    }
    .insight.teal   { border-color: #0EA58A; }
    .insight.pink   { border-color: #EE5FA7; }
    .insight.yellow { border-color: #F5B800; }
    .insight.purple { border-color: #7B5EA7; }
    .insight.red    { border-color: #EF4444; }
    .insight .icon  { font-size: 22px; margin-bottom: 8px; }
    .insight h3     { font-size: 13px; font-weight: 700; color: #111827; margin-bottom: 5px; }
    .insight p      { font-size: 12px; color: #6b7280; line-height: 1.6; }

    .neg-item {
      background: #FEF2F2; border-left: 3px solid #EF4444; border-radius: 6px;
      padding: 10px 14px; margin-bottom: 8px; font-size: 12px; color: #374151;
    }
    .neg-item .neg-meta { font-size: 11px; color: #6b7280; margin-bottom: 4px; font-weight: 600; }
    .neg-item .neg-text { line-height: 1.6; }

    .star-dist { display: flex; gap: 4px; align-items: center; }
    .star-seg  { height: 8px; border-radius: 2px; }

    footer { text-align: center; padding: 24px; font-size: 12px; color: #9ca3af; }
  </style>
</head>
<body>

<header>
  <div class="tag">FY27 Session Satisfaction Survey Analysis</div>
  <h1>⭐ Agentforce World Tour Korea 2026 — 세션 만족도 대시보드</h1>
  <p>행사일: 2026.06.09 &nbsp;|&nbsp; 51개 세션 만족도 분석 리포트 &nbsp;|&nbsp; 내년 행사 기획을 위한 인사이트 포함</p>
  <p style="margin-top:10px;font-size:13px;opacity:0.75;">✍️ 작성자: Nahea Han &nbsp;|&nbsp; <span style="background:rgba(239,68,68,0.25);color:#fca5a5;padding:2px 10px;border-radius:20px;font-weight:700;letter-spacing:0.06em;">🔒 CONFIDENTIAL</span></p>
</header>

<div class="main">

  <!-- KPI Row -->
  <div class="kpi-grid">
    <div class="kpi blue">
      <div class="label">총 세션 수</div>
      <div class="val">51</div>
      <div class="sub">키노트 1 + 트랙 50</div>
    </div>
    <div class="kpi teal">
      <div class="label">총 설문 응답 수</div>
      <div class="val">2,860</div>
      <div class="sub">전 세션 누적 응답</div>
    </div>
    <div class="kpi pink">
      <div class="label">전체 평균 CSAT</div>
      <div class="val">4.53</div>
      <div class="sub">5점 만점 기준</div>
    </div>
    <div class="kpi yellow">
      <div class="label">최다 응답 세션</div>
      <div class="val">699건</div>
      <div class="sub">KEYNOTE</div>
    </div>
    <div class="kpi purple">
      <div class="label">부정 피드백(★1-2)</div>
      <div class="val">52건</div>
      <div class="sub">전체 응답의 1.8%</div>
    </div>
  </div>

  <!-- Top Charts Row -->
  <div class="two-col">
    <div class="card">
      <div class="section-title">세션별 설문 참여 수 TOP 20</div>
      <canvas id="respondentChart"></canvas>
    </div>
    <div class="card">
      <div class="section-title">세션별 CSAT 분포 (전체 51개)</div>
      <canvas id="csatAllChart"></canvas>
    </div>
  </div>

  <!-- Grade dist + Track avg -->
  <div class="two-col">
    <div class="card">
      <div class="section-title">주요 세션 평점 분포 (★1~5 구성비)</div>
      <canvas id="distChart"></canvas>
    </div>
    <div class="card">
      <div class="section-title">트랙별 평균 CSAT 비교</div>
      <canvas id="trackCsatChart"></canvas>
    </div>
  </div>

  <!-- Insights -->
  <div class="section-title">🔍 시니어 마케터 인사이트 — 내년 행사를 위한 6가지 전략 권고</div>
  <div class="insight-grid">
    <div class="insight">
      <div class="icon">🏟️</div>
      <h3>좌석·공간 부족이 최대 불만 요인</h3>
      <p>키노트 응답자 7명 이상이 "<strong>자리가 부족해 외부에서 청취</strong>" 또는 "오디토리움 입장 불가"를 1점 사유로 기록. T06s3, T10s3도 동일 이슈. 내년엔 <strong>키노트 오디토리움 좌석 수용량 재검토</strong> 또는 별도 중계 공간 필수.</p>
    </div>
    <div class="insight teal">
      <div class="icon">🏆</div>
      <h3>송길영 작가 세션 — 만족도 최상위</h3>
      <p>T01s4 (송길영 작가) CSAT <strong>4.73</strong>, 부정 피드백 0건. T01s5 (패널토크) CSAT <strong>4.79</strong>. 실무 인사이트 + 명강사 조합이 핵심. 내년엔 <strong>유사 포맷 세션 추가 편성</strong> 권고.</p>
    </div>
    <div class="insight pink">
      <div class="icon">🔄</div>
      <h3>키노트 내용 중복 반복이 Track1 CSAT 저하</h3>
      <p>T01s2 (무신사) 부정 피드백 6건 중 다수가 "<strong>기조연설과 거의 같은 내용</strong>", "인사이트 없음". 키노트 고객사례 세션은 <strong>새로운 각도의 심층 내용</strong>으로 차별화 필요. 현재 구조는 중복 리스크 내재.</p>
    </div>
    <div class="insight yellow">
      <div class="icon">🎤</div>
      <h3>발표자 준비 미흡 — 프롬프트 낭독 다수 지적</h3>
      <p>키노트 및 T01s2에서 "<strong>발표자가 프롬프트만 읽음</strong>", "더듬음", "청중 전달 임팩트 부족" 다수. 내년엔 <strong>연사 사전 리허설 강화 + 발표 코칭 필수화</strong> 권고.</p>
    </div>
    <div class="insight purple">
      <div class="icon">📊</div>
      <h3>실무 사례 부족 — 가장 많이 나온 개선 요청</h3>
      <p>"실 사용정보가 없어 아쉬움", "구체적 사례 부족", "추상적" — T07s1·T03s1·T08s4·T01s2 등 다수 트랙에서 공통 지적. 내년엔 <strong>각 세션 필수 데모/실사례 포함 가이드라인</strong> 적용 권고.</p>
    </div>
    <div class="insight red">
      <div class="icon">🔊</div>
      <h3>AI 동시통역 품질 불만 — 영어 세션 한정</h3>
      <p>T06s1 "<strong>AI번역은 싱크가 맞지 않아서 안하느니만 못함</strong>", T07s2 "번역이 매끄럽지 않았습니다", 키노트 "영어 번역이 듣기 불편" 다수. 해외 연사 세션 AI 번역 품질 <strong>검토 및 인간 통역 병행 기준</strong> 재정립 필요.</p>
    </div>
  </div>

  <!-- Negative Feedback Section -->
  <div class="card" style="margin-bottom:28px;">
    <div class="section-title">🚨 부정 피드백 전체 목록 (★1–2점 의미있는 코멘트)</div>

    <div style="display:grid;grid-template-columns:1fr 1fr 1fr;gap:16px;margin-bottom:20px;">
      <div>
        <div style="font-size:12px;font-weight:700;color:#374151;margin-bottom:8px;padding:6px 10px;background:#FEF2F2;border-radius:6px;">🪑 공간·좌석 부족</div>
        <div class="neg-item"><div class="neg-meta">Keynote ★1</div><div class="neg-text">자리가 부족해서 외부에서 들었어요. 집중도가 너무 떨어져서 다음에는 수용 가능한 인원만 해주시면 좋겠습니다.</div></div>
        <div class="neg-item"><div class="neg-meta">Keynote ★1</div><div class="neg-text">오디토리움 자리가 한정적이라 못 들어갔어요</div></div>
        <div class="neg-item"><div class="neg-meta">T06s3 Snowflake ★2</div><div class="neg-text">자리가 넘 부족해서 중간이탈할수밖에</div></div>
      </div>
      <div>
        <div style="font-size:12px;font-weight:700;color:#374151;margin-bottom:8px;padding:6px 10px;background:#FEF2F2;border-radius:6px;">🎤 발표자 준비 미흡</div>
        <div class="neg-item"><div class="neg-meta">Keynote ★1</div><div class="neg-text">발표자는 프롬프트만 보고 발표....더듬음... 불필요한 인증용 사진촬영. 기조연설부터 이 투어의 신뢰성 및 기대가 매우 낮아짐.</div></div>
        <div class="neg-item"><div class="neg-meta">T01s2 무신사 ★1</div><div class="neg-text">준비된 프롬프트만 읽는게 아쉽</div></div>
        <div class="neg-item"><div class="neg-meta">T01s3 베인&컴퍼니 ★1</div><div class="neg-text">오늘 들은 세션 중에 가장 부실했음. 겉핥기식으로 ax하는 분들이 모인것같았음</div></div>
      </div>
      <div>
        <div style="font-size:12px;font-weight:700;color:#374151;margin-bottom:8px;padding:6px 10px;background:#FEF2F2;border-radius:6px;">🔄 내용 중복·인사이트 부족</div>
        <div class="neg-item"><div class="neg-meta">T01s2 무신사 ★2</div><div class="neg-text">기조연설과 거의 같은내용이어서 흥미가 떨어짐</div></div>
        <div class="neg-item"><div class="neg-meta">T01s2 무신사 ★2</div><div class="neg-text">아무런 인사이트가 없었습니다.</div></div>
        <div class="neg-item"><div class="neg-meta">T01s2 무신사 ★1</div><div class="neg-text">추상적</div></div>
      </div>
    </div>

    <div style="display:grid;grid-template-columns:1fr 1fr 1fr;gap:16px;">
      <div>
        <div style="font-size:12px;font-weight:700;color:#374151;margin-bottom:8px;padding:6px 10px;background:#FEF2F2;border-radius:6px;">📊 실무 사례 부족</div>
        <div class="neg-item"><div class="neg-meta">T07s1 데이터파운데이션 ★2</div><div class="neg-text">실 사용정보가 없어 아쉬웠어요</div></div>
        <div class="neg-item"><div class="neg-meta">T03s1 플랫폼 ★2</div><div class="neg-text">강연 내용이 너무 일반적이었습니다. 인사이트를 얻기 보단 잡코리아의 변화만 언급되서 아쉽습니다.</div></div>
        <div class="neg-item"><div class="neg-meta">T08s4 Admin 101 ★2</div><div class="neg-text">너무 내용이 쉽고 ax랑 관련성 모르겠어요</div></div>
        <div class="neg-item"><div class="neg-meta">T08s5 Tableau 101 ★2</div><div class="neg-text">실무 내용을 보고 싶었는데 학습 내용만</div></div>
      </div>
      <div>
        <div style="font-size:12px;font-weight:700;color:#374151;margin-bottom:8px;padding:6px 10px;background:#FEF2F2;border-radius:6px;">🔊 번역·언어 품질</div>
        <div class="neg-item"><div class="neg-meta">T06s1 영업 ★2</div><div class="neg-text">AI번역은 싱크가 맞지 않아서 안하느니만 못함.</div></div>
        <div class="neg-item"><div class="neg-meta">T07s2 플랫폼 ★2</div><div class="neg-text">번역이 매끄럽지 않았습니다</div></div>
        <div class="neg-item"><div class="neg-meta">Keynote ★1</div><div class="neg-text">강의 이전 1시간동안 기다리게 함.</div></div>
      </div>
      <div>
        <div style="font-size:12px;font-weight:700;color:#374151;margin-bottom:8px;padding:6px 10px;background:#FEF2F2;border-radius:6px;">📌 기타 개선 필요</div>
        <div class="neg-item"><div class="neg-meta">T03s1 플랫폼 ★1</div><div class="neg-text">crm얘기만이라서 실망</div></div>
        <div class="neg-item"><div class="neg-meta">T10s3 Women In Tech ★1</div><div class="neg-text">인터넷 짜깁기같은 느낌. 발표자가 너무 떠신다 해야하나, 설득력이 좀 떨어짐. 이것 저것 너무 찍먹이라 실전 프롬프팅 느낌 부족.</div></div>
        <div class="neg-item"><div class="neg-meta">T02s1 리테일 ★2</div><div class="neg-text">한계점도 소개해주셨으면 좋았을텐데 아쉽습니다.</div></div>
      </div>
    </div>
  </div>

  <!-- Full Session Table -->
  <div class="card" style="margin-bottom:28px;">
    <div class="section-title">📋 전체 세션 성과 상세 테이블 (51개)</div>
    <table>
      <thead>
        <tr>
          <th>세션 코드</th>
          <th>세션명</th>
          <th>트랙</th>
          <th>응답 수</th>
          <th>CSAT</th>
          <th>평점 분포</th>
          <th>부정(★1-2)</th>
        </tr>
      </thead>
      <tbody id="sessionTableBody"></tbody>
    </table>
  </div>

</div>

<footer>
  Agentforce World Tour Korea 2026 — Session Survey Analysis &nbsp;|&nbsp; 작성자: Nahea Han &nbsp;|&nbsp; 분석 기준일: 2026-06-26 &nbsp;|&nbsp; <strong style="color:#ef4444;">🔒 CONFIDENTIAL</strong>
</footer>

<script>
// ── DATA ─────────────────────────────────────────────────────────────────────
const sessions = [
  { sheet:"Keynote", name:"KEYNOTE",                                        track:"키노트",   respondents:699, csat:4.71, dist:{1:5,2:2,3:32,4:111,5:549}, neg:7 },
  { sheet:"T01s1",  name:"Keynote 고객사례 (포스코)",                         track:"Track 01", respondents:295, csat:4.66, dist:{1:2,2:1,3:15,4:59,5:218},  neg:3 },
  { sheet:"T01s2",  name:"Keynote 고객사례 (무신사)",                         track:"Track 01", respondents:202, csat:4.44, dist:{1:2,2:5,3:28,4:34,5:133},  neg:6 },
  { sheet:"T01s3",  name:"테크 인플루언서 인사이트 (베인앤컴퍼니)",           track:"Track 01", respondents:125, csat:4.54, dist:{1:1,2:0,3:15,4:24,5:85},   neg:1 },
  { sheet:"T01s4",  name:"테크 인플루언서 인사이트 (송길영 작가)",            track:"Track 01", respondents:91,  csat:4.73, dist:{1:0,2:0,3:9,4:7,5:75},     neg:0 },
  { sheet:"T01s5",  name:"테크 인플루언서 인사이트 (패널토크)",               track:"Track 01", respondents:28,  csat:4.79, dist:{1:0,2:0,3:1,4:4,5:23},     neg:0 },
  { sheet:"T02s1",  name:"리테일 및 소비재",                                  track:"Track 02", respondents:106, csat:4.55, dist:{1:0,2:3,3:11,4:17,5:75},   neg:2 },
  { sheet:"T02s2",  name:"제조 및 하이테크",                                  track:"Track 02", respondents:78,  csat:4.59, dist:{1:0,2:0,3:9,4:14,5:55},    neg:0 },
  { sheet:"T02s3",  name:"DAEU Nextier",                                     track:"Track 02", respondents:43,  csat:4.44, dist:{1:0,2:0,3:9,4:6,5:28},     neg:0 },
  { sheet:"T02s4",  name:"금융",                                             track:"Track 02", respondents:24,  csat:4.50, dist:{1:0,2:0,3:3,4:6,5:15},     neg:0 },
  { sheet:"T02s5",  name:"디지털 네이티브",                                   track:"Track 02", respondents:3,   csat:4.33, dist:{1:0,2:0,3:1,4:0,5:2},      neg:0 },
  { sheet:"T03s1",  name:"플랫폼",                                           track:"Track 03", respondents:58,  csat:4.45, dist:{1:1,2:1,3:8,4:9,5:39},     neg:2 },
  { sheet:"T03s2",  name:"제약 및 바이오",                                    track:"Track 03", respondents:42,  csat:4.40, dist:{1:0,2:0,3:8,4:9,5:25},     neg:0 },
  { sheet:"T03s3",  name:"I2MAX",                                            track:"Track 03", respondents:32,  csat:4.41, dist:{1:0,2:0,3:6,4:7,5:19},     neg:0 },
  { sheet:"T03s4",  name:"Customer Success",                                 track:"Track 03", respondents:28,  csat:4.71, dist:{1:0,2:1,3:1,4:3,5:23},     neg:1 },
  { sheet:"T03s5",  name:"Customer Zero",                                    track:"Track 03", respondents:5,   csat:4.60, dist:{1:0,2:0,3:0,4:2,5:3},      neg:0 },
  { sheet:"T04s1",  name:"에이전틱 애널리틱스",                               track:"Track 04", respondents:86,  csat:4.63, dist:{1:0,2:2,3:4,4:18,5:62},    neg:1 },
  { sheet:"T04s2",  name:"Tableau Next",                                     track:"Track 04", respondents:61,  csat:4.54, dist:{1:0,2:0,3:7,4:14,5:40},    neg:0 },
  { sheet:"T04s3",  name:"Tableau 고객사례1",                                 track:"Track 04", respondents:31,  csat:4.52, dist:{1:0,2:0,3:4,4:7,5:20},     neg:0 },
  { sheet:"T04s4",  name:"Tableau 고객사례2",                                 track:"Track 04", respondents:11,  csat:4.82, dist:{1:0,2:0,3:1,4:0,5:10},     neg:0 },
  { sheet:"T04s5",  name:"Tableau 커뮤니티",                                  track:"Track 04", respondents:7,   csat:5.00, dist:{1:0,2:0,3:0,4:0,5:7},      neg:0 },
  { sheet:"T05s1",  name:"에이전틱 CS",                                       track:"Track 05", respondents:61,  csat:4.72, dist:{1:0,2:0,3:4,4:9,5:48},     neg:0 },
  { sheet:"T05s2",  name:"Slack 고객사례 (크래프톤)",                          track:"Track 05", respondents:50,  csat:4.70, dist:{1:0,2:0,3:4,4:7,5:39},     neg:0 },
  { sheet:"T05s3",  name:"Slack Developer",                                  track:"Track 05", respondents:24,  csat:4.58, dist:{1:0,2:0,3:2,4:6,5:16},     neg:0 },
  { sheet:"T05s4",  name:"Slack 고객사례 (유니포스트)",                        track:"Track 05", respondents:25,  csat:4.80, dist:{1:0,2:0,3:1,4:3,5:21},     neg:0 },
  { sheet:"T05s5",  name:"Slack 로드맵",                                      track:"Track 05", respondents:10,  csat:4.70, dist:{1:0,2:0,3:0,4:3,5:7},      neg:0 },
  { sheet:"T06s1",  name:"영업",                                             track:"Track 06", respondents:57,  csat:4.61, dist:{1:0,2:1,3:4,4:11,5:41},    neg:1 },
  { sheet:"T06s2",  name:"서비스",                                           track:"Track 06", respondents:33,  csat:4.48, dist:{1:0,2:1,3:2,4:10,5:20},    neg:1 },
  { sheet:"T06s3",  name:"Snowflake 스폰서세션",                              track:"Track 06", respondents:48,  csat:4.46, dist:{1:0,2:2,3:6,4:8,5:32},     neg:2 },
  { sheet:"T06s4",  name:"마케팅",                                           track:"Track 06", respondents:15,  csat:4.53, dist:{1:0,2:0,3:3,4:1,5:11},     neg:0 },
  { sheet:"T06s5",  name:"커머스",                                           track:"Track 06", respondents:2,   csat:5.00, dist:{1:0,2:0,3:0,4:0,5:2},      neg:0 },
  { sheet:"T07s1",  name:"데이터 파운데이션",                                  track:"Track 07", respondents:63,  csat:4.44, dist:{1:0,2:2,3:8,4:13,5:40},    neg:2 },
  { sheet:"T07s2",  name:"플랫폼",                                           track:"Track 07", respondents:62,  csat:4.47, dist:{1:0,2:2,3:8,4:11,5:41},    neg:1 },
  { sheet:"T07s3",  name:"에이전틱 AI 플랫폼",                                track:"Track 07", respondents:25,  csat:4.32, dist:{1:0,2:0,3:8,4:1,5:16},     neg:0 },
  { sheet:"T07s4",  name:"보안",                                             track:"Track 07", respondents:15,  csat:4.47, dist:{1:0,2:1,3:2,4:1,5:11},     neg:1 },
  { sheet:"T07s5",  name:"뮬소프트",                                          track:"Track 07", respondents:3,   csat:4.33, dist:{1:0,2:0,3:1,4:0,5:2},      neg:0 },
  { sheet:"T08s1",  name:"Agentforce 101 — 러닝커브 대응전략",                track:"Track 08", respondents:42,  csat:4.67, dist:{1:0,2:0,3:4,4:6,5:32},     neg:0 },
  { sheet:"T08s2",  name:"Agentforce 101 — 핵심 매커니즘 Part1",             track:"Track 08", respondents:26,  csat:4.42, dist:{1:1,2:0,3:3,4:5,5:17},     neg:1 },
  { sheet:"T08s3",  name:"Agentforce 101 — 핵심 매커니즘 Part2",             track:"Track 08", respondents:18,  csat:4.39, dist:{1:1,2:0,3:3,4:1,5:13},     neg:1 },
  { sheet:"T08s4",  name:"Admin 101",                                        track:"Track 08", respondents:19,  csat:4.21, dist:{1:1,2:1,3:3,4:2,5:12},     neg:2 },
  { sheet:"T08s5",  name:"Tableau 101",                                      track:"Track 08", respondents:3,   csat:4.00, dist:{1:0,2:1,3:0,4:0,5:2},      neg:1 },
  { sheet:"T09s1",  name:"스타트업 혁신 사례",                                 track:"Track 09", respondents:42,  csat:4.36, dist:{1:1,2:1,3:7,4:6,5:27},     neg:2 },
  { sheet:"T09s2",  name:"세일즈포스벤처스",                                   track:"Track 09", respondents:20,  csat:4.30, dist:{1:0,2:0,3:4,4:6,5:10},     neg:0 },
  { sheet:"T09s3",  name:"세일즈포스벤처스 투자 스타트업",                      track:"Track 09", respondents:17,  csat:4.24, dist:{1:0,2:0,3:4,4:5,5:8},      neg:0 },
  { sheet:"T09s4",  name:"K-뷰티 스타트업 및 중견강소",                        track:"Track 09", respondents:14,  csat:4.50, dist:{1:0,2:0,3:2,4:3,5:9},      neg:0 },
  { sheet:"T09s5",  name:"스타트업 Customer Zero",                            track:"Track 09", respondents:6,   csat:4.83, dist:{1:0,2:0,3:0,4:1,5:5},      neg:0 },
  { sheet:"T10s1",  name:"골든후디 패널토크",                                  track:"Track 10", respondents:22,  csat:4.50, dist:{1:0,2:1,3:3,4:2,5:16},     neg:1 },
  { sheet:"T10s2",  name:"마케터 커뮤니티",                                   track:"Track 10", respondents:44,  csat:4.50, dist:{1:0,2:1,3:6,4:7,5:30},     neg:1 },
  { sheet:"T10s3",  name:"Women In Tech 커뮤니티",                            track:"Track 10", respondents:24,  csat:4.29, dist:{1:1,2:0,3:6,4:1,5:16},     neg:1 },
  { sheet:"T10s4",  name:"개발자 커뮤니티",                                   track:"Track 10", respondents:10,  csat:4.30, dist:{1:0,2:0,3:3,4:1,5:6},      neg:0 },
  { sheet:"T10s5",  name:"어드민 커뮤니티",                                   track:"Track 10", respondents:5,   csat:5.00, dist:{1:0,2:0,3:0,4:0,5:5},      neg:0 },
];

const trackColors = {
  '키노트':   '#066AFE',
  'Track 01': '#0EA58A',
  'Track 02': '#EE5FA7',
  'Track 03': '#F5B800',
  'Track 04': '#7B5EA7',
  'Track 05': '#0ea5e9',
  'Track 06': '#6366f1',
  'Track 07': '#f97316',
  'Track 08': '#14b8a6',
  'Track 09': '#a855f7',
  'Track 10': '#ec4899',
};

// ── Chart 1: Top 20 respondents (horizontal bar) ─────────────────────────────
const top20 = [...sessions].sort((a,b) => b.respondents - a.respondents).slice(0, 20);
new Chart(document.getElementById('respondentChart'), {
  type: 'bar',
  data: {
    labels: top20.map(s => s.name.length > 22 ? s.name.slice(0,22)+'…' : s.name),
    datasets: [{
      label: '설문 응답 수',
      data: top20.map(s => s.respondents),
      backgroundColor: top20.map(s => trackColors[s.track] || '#066AFE'),
      borderRadius: 4,
    }]
  },
  options: {
    responsive: true, indexAxis: 'y',
    plugins: { legend: { display: false }, tooltip: { callbacks: { label: ctx => ` ${ctx.raw}명 응답 (${ctx.label})` } } },
    scales: {
      x: { grid: { color: '#f3f4f6' } },
      y: { ticks: { font: { size: 10 } } }
    }
  }
});

// ── Chart 2: CSAT scatter all 51 ──────────────────────────────────────────────
new Chart(document.getElementById('csatAllChart'), {
  type: 'bar',
  data: {
    labels: sessions.map(s => s.sheet),
    datasets: [{
      label: 'CSAT',
      data: sessions.map(s => s.csat),
      backgroundColor: sessions.map(s => {
        if (s.csat >= 4.7) return '#16a34a';
        if (s.csat >= 4.5) return '#066AFE';
        if (s.csat >= 4.3) return '#F5B800';
        return '#EF4444';
      }),
      borderRadius: 3,
    }]
  },
  options: {
    responsive: true,
    plugins: {
      legend: { display: false },
      tooltip: { callbacks: { label: ctx => ` ${sessions[ctx.dataIndex].name}: CSAT ${ctx.raw}` } }
    },
    scales: {
      y: { min: 3.8, max: 5.1, title: { display: true, text: 'CSAT (5점 만점)' } },
      x: { ticks: { font: { size: 8 }, maxRotation: 90 } }
    }
  }
});

// ── Chart 3: Grade distribution stacked bar (top 10 by respondents) ──────────
const top10 = [...sessions].sort((a,b) => b.respondents - a.respondents).slice(0, 10);
new Chart(document.getElementById('distChart'), {
  type: 'bar',
  data: {
    labels: top10.map(s => s.name.length > 20 ? s.name.slice(0,20)+'…' : s.name),
    datasets: [
      { label:'★1', data: top10.map(s => s.dist[1]), backgroundColor:'#EF4444', borderRadius:0 },
      { label:'★2', data: top10.map(s => s.dist[2]), backgroundColor:'#f97316', borderRadius:0 },
      { label:'★3', data: top10.map(s => s.dist[3]), backgroundColor:'#F5B800', borderRadius:0 },
      { label:'★4', data: top10.map(s => s.dist[4]), backgroundColor:'#0ea5e9', borderRadius:0 },
      { label:'★5', data: top10.map(s => s.dist[5]), backgroundColor:'#16a34a', borderRadius:3 },
    ]
  },
  options: {
    responsive: true, indexAxis: 'y',
    plugins: { legend: { position: 'top', labels: { font: { size: 11 } } } },
    scales: {
      x: { stacked: true, grid: { color: '#f3f4f6' } },
      y: { stacked: true, ticks: { font: { size: 10 } } }
    }
  }
});

// ── Chart 4: Track avg CSAT ───────────────────────────────────────────────────
const trackData = {};
sessions.forEach(s => {
  if (!trackData[s.track]) trackData[s.track] = [];
  trackData[s.track].push(s.csat);
});
const trackLabels = Object.keys(trackData);
const trackAvgs   = trackLabels.map(t => +(trackData[t].reduce((a,b)=>a+b,0)/trackData[t].length).toFixed(2));
new Chart(document.getElementById('trackCsatChart'), {
  type: 'bar',
  data: {
    labels: trackLabels,
    datasets: [{
      label: '트랙 평균 CSAT',
      data: trackAvgs,
      backgroundColor: trackLabels.map(t => trackColors[t] || '#066AFE'),
      borderRadius: 5,
    }]
  },
  options: {
    responsive: true,
    plugins: { legend: { display: false }, tooltip: { callbacks: { label: ctx => ` 평균 CSAT: ${ctx.raw}` } } },
    scales: {
      y: { min: 4.0, max: 5.0, title: { display: true, text: 'CSAT' } },
      x: { ticks: { font: { size: 11 } } }
    }
  }
});

// ── Table ─────────────────────────────────────────────────────────────────────
const tbody = document.getElementById('sessionTableBody');
sessions.forEach(s => {
  const total = s.respondents;
  const pct = k => total ? Math.round(s.dist[k]/total*100) : 0;
  const csatColor = s.csat >= 4.7 ? 'good' : s.csat >= 4.4 ? '' : s.csat >= 4.2 ? 'warn' : 'bad';
  const trackBadge = {
    '키노트':'badge-blue','Track 01':'badge-green','Track 02':'badge-pink',
    'Track 03':'badge-orange','Track 04':'badge-purple','Track 05':'badge-blue',
    'Track 06':'badge-purple','Track 07':'badge-orange','Track 08':'badge-green',
    'Track 09':'badge-pink','Track 10':'badge-gray'
  }[s.track] || 'badge-gray';

  const barHtml = `
    <div class="star-dist">
      ${[5,4,3,2,1].map(k => `<div class="star-seg" title="★${k}: ${s.dist[k]}명 (${pct(k)}%)" style="width:${Math.max(pct(k),1)}px;background:${['','#EF4444','#f97316','#F5B800','#0ea5e9','#16a34a'][k]};"></div>`).join('')}
      <span style="font-size:10px;color:#9ca3af;white-space:nowrap;">${pct(5)}%★5</span>
    </div>`;

  const negBadge = s.neg > 0
    ? `<span class="badge badge-red">${s.neg}건</span>`
    : `<span class="badge badge-green">0건</span>`;

  tbody.innerHTML += `
    <tr>
      <td><code style="font-size:11px;color:#6b7280;">${s.sheet}</code></td>
      <td style="font-size:12px;">${s.name}</td>
      <td><span class="badge ${trackBadge}">${s.track}</span></td>
      <td><strong>${s.respondents.toLocaleString()}</strong></td>
      <td class="${csatColor}"><strong>${s.csat}</strong></td>
      <td>${barHtml}</td>
      <td>${negBadge}</td>
    </tr>`;
});
</script>
</body>
</html>
