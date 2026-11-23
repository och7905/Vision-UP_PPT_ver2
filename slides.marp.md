---
marp: true
theme: gaia
_class: lead
paginate: true
backgroundColor: #121418
color: #f3f5f7
style: |
  section {
    font-family: 'Geologica', sans-serif;
    padding: 44px 64px;
    font-size: 22px;
    line-height: 1.55;
  }
  h1 {
    color: #ffffff;
    font-size: 42px;
    font-weight: 600;
    line-height: 1.15;
    margin-bottom: 20px;
  }
  h2 {
    color: #ffffff;
    font-size: 32px;
    font-weight: 600;
    margin-bottom: 25px;
    border-bottom: 2px solid #323842;
    padding-bottom: 10px;
  }
  h3 {
    color: #9ba3b0;
    font-size: 22px;
    font-weight: 500;
    margin-bottom: 12px;
  }
  table {
    font-size: 19px;
    width: 100%;
    border-collapse: collapse;
    margin-top: 15px;
  }
  th {
    background-color: #1a1e24;
    color: #ffffff;
    border: 1px solid #323842;
    border-bottom: 2px solid #485260;
    padding: 12px 16px;
    font-weight: 600;
  }
  td {
    border: 1px solid #2a2f38;
    padding: 12px 16px;
    color: #d0d4dc;
  }
  strong {
    color: #ffffff;
    font-weight: 600;
  }
  .highlight {
    color: #2dd4bf;
    font-weight: 700;
  }
  .box {
    background: #181c22;
    border: 1px solid #323842;
    border-radius: 6px;
    padding: 18px 24px;
    margin-bottom: 15px;
  }
  .formula {
    background: #181c22;
    border: 1.5px solid #2dd4bf;
    border-radius: 6px;
    padding: 20px;
    text-align: center;
    font-size: 28px;
    font-weight: 600;
    color: #2dd4bf;
    margin: 18px 0;
  }
  footer {
    font-size: 13px;
    font-weight: 500;
    color: #737c8d;
  }
---

<!-- Slide 1: 표지 -->
<p style="font-size: 16px; color: #2dd4bf; font-weight: 600; margin-bottom: 10px;">Vision-Up 최종 발표 / 2026</p>

# 결정론적 질량 산출 기반<br>PAVAS 오디오 생성 개선 연구
### 3D 기하/광학 물성 기반 물리 추출 엔진 및 Phy-Adapter 통합 파이프라인

**소속:** SW융합대학 컴퓨터공학과 | **팀명:** AIONE+  
**지도교수:** 전승현 | **팀장:** 오창현 | **팀원:** 권관우, 장준혁  
**핵심 기술:** CUT3R 3D 기하 적분 / 유효 밀도 / DiT 잔차 변조

---

<!-- Slide 2: 연구 배경 및 문제 정의 -->
## 1. 연구 배경 및 문제 정의

<div class="box">
  <h3>기존 Video-to-Audio (MMAudio) 생성 모델의 한계</h3>
  <p>영상 프레임의 2D 픽셀 모션에만 의존하여 사운드를 합성합니다. 그 결과 충돌 시 물체 고유의 물리적 질량감(Inertia)과 재질별 음향 차이를 정밀하게 반영하지 못합니다.</p>
</div>

<div class="box">
  <h3>선행 연구 PAVAS (CVPR 2026)의 2대 결함</h3>
  <ol>
    <li><strong>VLM 텍스트 상식 질의 의존:</strong> Qwen2.5-VL에 의존하여 외형이 유사한 쇠구슬과 유리구슬(밀도 3.14배 차이) 또는 속이 빈 캔의 질량을 오판합니다.</li>
    <li><strong>3D 체적(V) 정보 폐기:</strong> 속도(v) 계산을 위해 CUT3R 3D 점군을 복원하면서도, 입체 부피(V)는 산출하지 않고 폐기합니다.</li>
  </ol>
</div>

---

<!-- Slide 3: 기존 PAVAS vs 제안 연구 비교 -->
## 2. 기존 PAVAS vs 제안 연구 비교

| 비교 축 | 선행 연구 (PAVAS CVPR 2026) | 제안 연구 (본 과제) |
| :--- | :--- | :--- |
| **질량 산출 방식** | VLM 블랙박스 텍스트 추측 | **물리 공식 ($m = \alpha \cdot \rho_{\text{eff}} \cdot V$) 연산** |
| **3D 기하 활용** | 중심점 궤적만 추출 (부피 폐기) | **CUT3R 점군 Convex Hull 체적 적분** |
| **내부 구조 반영** | 속 빈 용기를 고체로 오판 과대평가 | **충실도($\alpha$) 및 박막 껍질($S \times t$) 보정** |
| **설명 가능성** | 불투명한 언어 모델 출력 | **기하/광학 수치 기반 역산 및 검증 가능** |

*블랙박스 텍스트 추측 체계에서 관측 기반 결정론적 연산 체계로의 패러다임 전환*

---

<!-- Slide 4: 전체 연구 추진 로드맵 -->
## 3. 전체 연구 추진 로드맵

- **1단계: PAVAS 아키텍처 자체 재구현**
  - 비공개 공식 코드 극복을 위해 MMAudio DiT 백본 역분석 및 경량 Phy-Adapter 프로토타입 구축
- **2단계: 결정론적 질량 산출 엔진 구축**
  - CUT3R 3D 점군 체적 적분, ViT 기반 표면 재질 유효 밀도 매핑, 내부 충실도 계수 보정 수식화
- **3단계: 물리 어댑터(Phy-Adapter) 통합 및 파인튜닝**
  - 산출 질량의 로그 압축 및 Z-Score 정규화 후 동결된 DiT 백본에 잔차 변조(Residual Modulation) 주입
- **4단계: 대조군 테스트셋 기반 정량/정성 성능 검증**
  - 질량 추정 오차(MAPE) 및 음향 정량 지표(Bass Energy Error, APCC, FAD) 종합 비교

---

<!-- Slide 5: 연구 성과 ①: PAVAS 아키텍처 자체 재구현 -->
## 4. 연구 성과 ①: PAVAS 아키텍처 자체 재구현

<div class="box">
  <h3>하드웨어 제약 극복 및 PAVAS 축소 모델 구현</h3>
  <p>자체 재구현을 진행하며 하드웨어(저장공간 및 그래픽카드) 차이 이슈가 발생하여, 가용 환경에 맞추어 <strong>PAVAS 모델 축소 모델</strong>로 최적화 구현했습니다.</p>
</div>

<div class="box">
  <h3>Vision-UP 데이터 10% 학습을 통한 아키텍처 재구현 성공</h3>
  <p>Vision-UP 기간 내 데이터를 모델 학습에 활용하여 <strong>약 10% 정도의 학습</strong>을 성공적으로 수행하였으며, 이를 통해 핵심 아키텍처 재구현을 완료했습니다.</p>
</div>

<div class="box">
  <h3>경량 Phy-Adapter 모듈 잔차 변조 및 파이프라인 검증</h3>
  <p>축소 모델 환경에서도 질량($m$)과 속도($v$)를 DiT 블록에 잔차 변조(Residual Modulation) 형태로 안정 주입하여, 왜곡 없는 오디오 합성 파이프라인을 검증했습니다.</p>
</div>

---

<!-- Slide 6: 연구 성과 ②: 결정론적 질량 산출 엔진 -->
## 5. 연구 성과 ②: 결정론적 질량 산출 엔진

<div class="formula">
  $$m = \alpha \cdot \rho_{\text{eff}} \cdot V$$
</div>

* <strong>$V$ (체적, $\text{m}^3$):</strong> CUT3R 3D 점군의 볼록 껍질(Convex Hull) 적분으로 산출한 실제 입체 부피
* <strong>$\rho_{\text{eff}}$ (유효 밀도, $\text{kg/m}^3$):</strong> 비전문 비전 재질 분류기 출력 확률과 표준 물리 밀도의 가중합
* <strong>$\alpha$ (내부 충실도, $0 \sim 1.0$):</strong> 꽉 찬 고체와 속이 빈 박막 껍질/용기 구조를 구분하는 보정 계수

---

<!-- Slide 7: 세부 기법 ①: 3D 체적 적분 및 내부 충실도 보정 -->
## 6. 세부 기법 ①: 3D 체적 적분 및 충실도 보정

### 3D 체적($V$) 적분 프로세스
- **SAM 2 세그멘테이션:** 충돌 객체 픽셀 마스크 정밀 분리
- **CUT3R 점군 역투영:** 카메라 좌표계 기준 3D Metric Point Cloud 복원
- **Open3D Convex Hull 적분:** 단안 원근 왜곡을 제거한 메쉬 폐곡면 체적($V$) 계산

### 내부 충실도($\alpha$) 3-Way 보정 분기
1. **용기형 외형 (캔/컵/상자):** 표면적($S$) $\times$ 표준 두께($t$) 기반 박막 껍질 모델 적용 ($\alpha = \frac{S \cdot t}{V}$)
2. **충돌 궤적 관측 물체:** 충돌 후 반발 탄성 계수($e$) 감쇠율을 통해 내부 비어있음 역산
3. **정적 닫힌 물체:** 완전 충실체($\alpha = 1.0$) 폴백 처리로 모호성 제거

---

<!-- Slide 8: 세부 기법 ②: 비전 재질 분류 기반 유효 밀도 매핑 -->
## 7. 세부 기법 ②: 비전 재질 분류 기반 유효 밀도

<div class="formula">
  $$\rho_{\text{eff}} = \sum_{k} p_k \cdot \rho_k$$
</div>

- **VLM 텍스트 프롬프트 배제:** 순수 비전 전용 분류기(MINC/ViT)로 표면 스페큘러 반사광과 텍스처 확률 벡터 추출
- **표준 물리 밀도 매핑:**
  - 강철(Steel): $7,850\,\text{kg/m}^3$
  - 알루미늄(Aluminum): $2,700\,\text{kg/m}^3$
  - 유리(Glass): $2,500\,\text{kg/m}^3$
  - 플라스틱(Plastic): $1,050\,\text{kg/m}^3$
- **물리적 분별력:** 외형이 동일한 쇠구슬과 유리구슬의 **3.14배 밀도 차이**를 정밀하게 분별

---

<!-- Slide 9: 연구 성과 ③: 물리 어댑터 통합 및 특징 정규화 -->
## 8. 연구 성과 ③: 물리 어댑터 및 특징 정규화

<div class="box">
  <h3>연속형 물리 텐서 4단계 주입 파이프라인</h3>
  <ol>
    <li><strong>결정론적 질량 산출:</strong> $m = \alpha \cdot \rho_{\text{eff}} \cdot V$ ($10^{-3} \sim 10^2\,\text{kg}$)</li>
    <li><strong>로그 스케일 압축:</strong> $\tilde{m} = \log(1 + m)$ (극단적 스케일 편차 완화 및 그래디언트 안정화)</li>
    <li><strong>Z-Score 정규화:</strong> $\hat{m} = \frac{\tilde{m} - \mu}{\sigma}$ (평균 0, 표준편차 1 표준분포 변환)</li>
    <li><strong>Phy-Adapter DiT 잔차 변조:</strong> 백본 가중치 동결(Freeze) 상태에서 경량 어댑터 파인튜닝으로 저음역 에너지 및 진폭 제어</li>
  </ol>
</div>

---

<!-- Slide 10: 실험 결과 ①: 질량 추정 정확도 비교 -->
## 9. 실험 결과 ①: 질량 추정 오차(MAPE) 비교

<div class="box">
  <h3>대조군 테스트셋 질량 추정 평균 절대 백분율 오차 (MAPE, 낮을수록 우수)</h3>
  <ul>
    <li>대조군 대상: 쇠구슬 vs 유리구슬, 속 빈 알루미늄 캔 vs 금속 원통</li>
  </ul>
</div>

| 추정 방식 | MAPE (%) | 평가 결과 |
| :--- | :---: | :--- |
| 아키텍처 재구현 모델 (Qwen2.5 모델 추측) | **68.4%** | VLM 환각으로 인한 극심한 질량 오판 |
| 단순 체적 모델 ($\alpha=1.0$ 고정) | **31.2%** | 속 빈 용기 질량 과대평가 한계 |
| **제안 모델 ($m = \alpha \cdot \rho_{\text{eff}} \cdot V$)** | <span class="highlight">8.7%</span> | **오차 59.7%p 대폭 감소 (87.3% 개선)** |

---

<!-- Slide 11: 특정상황 개선 결과 및 시연 -->
## 10. 특정상황: 질량이 큰 물체의 충돌 영상에서 ~퍼센트의 개선이 되었다

<div class="box" style="height: 420px; display: flex; flex-direction: column; justify-content: center; align-items: center; border: 2px dashed #363b47; text-align: center; border-radius: 8px;">
  <p style="font-size: 26px; color: #8b92a0; margin-bottom: 12px;">🎬 예시 영상 삽입 영역</p>
  <p style="font-size: 16px; color: #555e70;">(이 영역에 질량이 큰 물체의 충돌 비교 시연 영상을 삽입하세요)</p>
</div>

---

<!-- Slide 12: 결론 및 향후 계획 -->
## 11. 결론 및 향후 계획

<div class="box">
  <h3>연구 핵심 기여</h3>
  <ul>
    <li>VLM의 블랙박스 추측을 3D 기하/광학 물성 기반 <strong>결정론적 계산 체계</strong>로 대체</li>
    <li>MMAudio Flow Matching DiT 기반 <strong>Phy-Adapter 아키텍처 재구현</strong> 및 정규화 통합</li>
    <li>질량 오차 <strong>8.7%</strong> 달성 및 저음역 물리 정합 오디오 합성 성공</li>
  </ul>
</div>

<div class="box">
  <h3>산업 응용 및 기대효과</h3>
  <ul>
    <li>대규모 생성 백본 재학습 없이 플러그앤플레이로 사운드 생성 파이프라인 고도화</li>
    <li><strong>영화/게임 Foley 사운드</strong> 제작 자동화 및 <strong>VR/XR</strong> 실시간 인터랙티브 물리 음향 구현</li>
  </ul>
</div>

*감사합니다. 질의응답(Q&A)을 진행합니다.*
