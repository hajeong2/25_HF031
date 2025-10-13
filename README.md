## **1. 프로젝트 개요**

**1-1. 프로젝트 소개**
- 프로젝트 명 : 건물 균열 탐지 수직 자율주행 로봇
- 프로젝트 정의 : 고층 외벽의 균열 탐지 수직 자율주행 로봇

**1-2. 개발 배경 및 필요성**
- 작업자의 고소작업 위험성과 접근성 한계로 외벽 점검의 자동화 기술 수요 증가
- 기상 제약/지속성 한계로 드론/인력 기반 점검의 기술적 제약 존재
- 협소한 외벽 환경에서 정밀/실시간 점검 필요성에 다라 자동화 기술 도입 요구 증가
- 사생활 침해 문제 해결

**1-3. 프로젝트 특장점**
- 드론/인력으로 구현하기 어려운 수직 자율주행 방식의 고층·협소 외벽 자동 점검
- 재질에 구애받지 않는 진공 흡착 주행 방식으로 안정적이고 일관된 외벽 주행 가능
- 외벽 손상을 실시간 분석·판단하는 AI 내장형 진단 시스템

**1-4. 주요 기능**
- 수직 자율주행 기능을 통해 고층 및 협소 공간 외벽을 이동하며 점검수행
- 진공 흡착 방식으로 다양한 외장재에서도 강한 밀착력과 안정적인 벽면 주행 가능
- AI 기반 영상 분석으로 외벽의 균열·오염 감지 및 심각도 분류 자동화

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
| <img width="100" height="120" alt="Image" src="https://github.com/user-attachments/assets/e5ed992b-891d-4763-8339-85117cc62ebd" /> | <img width="80" height="100" alt="Image" src="https://github.com/user-attachments/assets/6dc10dc4-fd4e-4e7b-9b7f-d91b70599bf5" /> | <img width="100" height="110" alt="Image" src="https://github.com/user-attachments/assets/2efa44a6-043f-44b4-a4ab-5ebec4665f8e" /> | <img width="80" height="100" alt="image" src="https://github.com/user-attachments/assets/0d5909f0-fc73-4ab9-be09-4d48e3e71083" > | <img width="80" height="100" alt="image" src="https://github.com/user-attachments/assets/c7f66b7c-ab84-41fa-8fba-b49dba28b677" > |
|:---:|:---:|:---:|:---:|:---:|
| **이지해** | **김다나** | **김하정** | **박유라** | **곽문기** |
| • 프론트엔드 <br> • 관제 | • 하드웨어 <br> • 임베디드 제어  | • AI 분석 <br> • 시스템 통합 |• 데이터 베이스 <br> • ... | • 프로젝트 멘토 <br> • 기술 자문 |



---
## **3. 시스템 구성도**
> **(참고)** S/W구성도, H/W구성도, 서비스 흐름도 등을 작성합니다. 시스템의 동작 과정 등을 추가할 수도 있습니다.
- 서비스 흐름도 (*DB 추가 예정)
<img width="600" height="400" alt="Image" src="https://github.com/user-attachments/assets/bd6d5aa0-424b-4c5d-a3d3-82fafd0fdde8" />


- 소프트웨어 구성도
<img width="600" height="400" alt="Image" src="https://github.com/user-attachments/assets/4620c0b4-4e30-4369-86ed-55287085fb6d" />


- 하드웨어 구성도
<img width="600" height="400" alt="Image" src="https://github.com/user-attachments/assets/30f4d73b-b8d4-4a60-986a-61505a201d11" />

<img width="500" height="400" alt="Image" src="https://github.com/user-attachments/assets/815ae5bd-3623-4f9d-bc91-884a647e0086" />


---
## **4. 작품 소개영상**
[![한이음 드림업 프로젝트 소개](https://img.youtube.com/vi/gbGASSssgFE/maxresdefault.jpg)](https://youtu.be/gbGASSssgFE)


---
## **5. 핵심 소스코드**
- 소스코드 설명 : API를 활용해서 자동 배포를 생성하는 메서드입니다.

```python
    private static void start_deployment(JsonObject jsonObject) {
        String user = jsonObject.get("user").getAsJsonObject().get("login").getAsString();
        Map<String, String> map = new HashMap<>();
        map.put("environment", "QA");
        map.put("deploy_user", user);
        Gson gson = new Gson();
        String payload = gson.toJson(map);

        try {
            GitHub gitHub = GitHubBuilder.fromEnvironment().build();
            GHRepository repository = gitHub.getRepository(
                    jsonObject.get("head").getAsJsonObject()
                            .get("repo").getAsJsonObject()
                            .get("full_name").getAsString());
            GHDeployment deployment =
                    new GHDeploymentBuilder(
                            repository,
                            jsonObject.get("head").getAsJsonObject().get("sha").getAsString()
                    ).description("Auto Deploy after merge").payload(payload).autoMerge(false).create();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```
