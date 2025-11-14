## **1. 프로젝트 개요**

**1-1. 프로젝트 소개**
- 프로젝트 명 : 건물 균열 탐지 수직 주행 로봇
- 프로젝트 정의 : 고층 외벽의 균열 탐지 수직 주행 로봇

**1-2. 개발 배경 및 필요성**
- 작업자의 고소작업 위험성과 접근성 한계로 외벽 점검의 자동화 기술 수요 증가
- 기상 제약/지속성 한계로 드론/인력 기반 점검의 기술적 제약 존재
- 협소한 외벽 환경에서 정밀/실시간 점검 필요성에 다라 자동화 기술 도입 요구 증가
- 사생활 침해 문제 해결

**1-3. 프로젝트 특장점**
- 드론/인력으로 구현하기 어려운 수직 주행 방식의 고층·협소 외벽 자동 점검
- 재질에 구애받지 않는 진공 흡착 주행 방식으로 안정적이고 일관된 외벽 주행 가능
- 외벽 손상을 실시간 분석·판단하는 AI 내장형 진단 시스템

**1-4. 주요 기능**
- 수직 주행 기능을 통해 고층 및 협소 공간 외벽을 이동하며 점검수행
- 진공 흡착 방식으로 다양한 외장재에서도 강한 밀착력과 안정적인 벽면 주행 가능
- AI 기반 영상 분석으로 외벽의 균열 감지

**1-5. 기대 효과 및 활용 분야**
- [비용절감] 대형 구조물 외벽 점검에 활용되어 고위험 작업의 인건비 등 비용 절감
- [점검 사각지대 해소] 건물 밀집 지역에서도 적용 가능해 유지관리 사각지대 해소
- [적용 대상 다각화] 다양한 공공/산업시설 정기 유지관리 업무에 효율/효과적

**1-6. 기술 스택**
- 프론트엔드 : Node-RED Dashboard
- 백엔드 : Python, Node-RED
- AI/ML : YOLOv5(PyTorch 기반), OpenCV, Numpy, Torch, CUDA, cuDNN
- 데이터베이스 : MySQL
- 클라우드 : Ngrok
- 배포 및 관리 : Git

---

## **2. 팀원 소개**
| <img width="80" height="100" alt="Image" src="https://github.com/user-attachments/assets/6dc10dc4-fd4e-4e7b-9b7f-d91b70599bf5" /> | <img width="100" height="110" alt="Image" src="https://github.com/user-attachments/assets/2efa44a6-043f-44b4-a4ab-5ebec4665f8e" /> | <img width="100" height="120" alt="Image" src="https://github.com/user-attachments/assets/e5ed992b-891d-4763-8339-85117cc62ebd" /> | <img width="80" height="100" alt="image" src="https://github.com/user-attachments/assets/c7f66b7c-ab84-41fa-8fba-b49dba28b677" > |
|:---:|:---:|:---:|:---:|
| **김다나** | **김하정** | **이지해** | **곽문기** |
| • 하드웨어 <br> • DB  | • AI 분석 <br> • 시스템 통합 |• 프론트엔드 <br> • 관제 | • 프로젝트 멘토 <br> • 기술 자문 |



---
## **3. 시스템 구성도**
- 서비스 흐름도
<img width="600" height="400" alt="Image" src="https://github.com/user-attachments/assets/bd6d5aa0-424b-4c5d-a3d3-82fafd0fdde8" />


- 소프트웨어 구성도
<img width="600" height="400" alt="Image" src="https://github.com/user-attachments/assets/b42bc48d-2258-45de-8395-7eadc443c3fb" />


- 하드웨어 구성도
<img width="600" height="400" alt="Image" src="https://github.com/user-attachments/assets/93d4adba-0009-4b2d-bfa4-aa1c20da22b1" />

<img width="600" height="400" alt="Image" src="https://github.com/user-attachments/assets/2dccd1b3-8f3f-4254-b5f1-645bb89fcfa0" />


---
## **4. 작품 소개영상**
[![한이음 드림업 프로젝트 소개](https://img.youtube.com/vi/gbGASSssgFE/maxresdefault.jpg)](https://youtu.be/gbGASSssgFE)


---
## **5. 핵심 소스코드**
- 바퀴 회전 감지로 이동거리 계산

```C++
    inline void updateGateOne(Gate& g, int pin){
  uint32_t nowMs = millis();
  uint32_t nowUs = micros();

  if (!g.warmed){
    if (g.warmStartMs==0) g.warmStartMs = nowMs;
    if (nowMs - g.warmStartMs >= WARMUP_MS){
      g.warmed = true;
      g.lastLevel = digitalRead(pin);
      if (g.lastLevel==HIGH) g.highStart=nowUs; else g.lowStart=nowUs;
    } else {
      return;
    }
  }

  int lvl = digitalRead(pin);

  if (lvl==HIGH){
    if (g.lastLevel==LOW){ g.highStart=nowUs; g.lowStart=0; }
    else if (!g.armed && g.highStart && (nowUs - g.highStart)>=HIGH_MIN_US){
      g.armed = true;
    }
  } else {
    if (g.lastLevel==HIGH){ g.lowStart = g.armed ? nowUs : 0; }
    else if (g.armed && g.lowStart && (nowUs - g.lowStart)>=LOW_MIN_US){
      g.ticksTot++; g.ticksAcc++;
      g.armed = false; g.lowStart=0;
    }
  }
  g.lastLevel = lvl;
}
```

- IMU + 엔코더 융합, 정확한 방향 추정

```C++
   // 바이어스 제거 (yaw만 쓸거라 gz만 사용)
gz -= bgz;
// 움직임 판단(간단: 자이로)
bool moving = (fabsf(gz) > ZUPT_GYRO_THR);
// yaw 적분(+데드밴드/스케일)
float gz_dps = gz * RAD_TO_DEG;
if (fabsf(gz_dps) < GZ_DEADBAND_DPS) gz = 0.0f;
yaw_imu = wrapPI(yaw_imu + (YAW_SCALE * gz) * dt);

// 엔코더 기반 이동/회전
float ds_l = dL * METERS_PER_TICK;
float ds_r = dR * METERS_PER_TICK;
float ds_c = 0.5f * (ds_l + ds_r);
float dtheta_enc = (ds_r - ds_l) / WHEEL_BASE_M;
yaw_enc = wrapPI(yaw_enc + dtheta_enc);

// 퓨전 yaw: 자이로 예측 + 엔코더 느린 보정
yaw_f = wrapPI(yaw_f + (YAW_SCALE * gz) * dt);
float err = wrapPI(yaw_enc - yaw_f);
float Kc  = ((dL+dR)>0 || moving) ? KC_MOVE : KC_STOP;
yaw_f = wrapPI(yaw_f + Kc * err);
```
