# 🔫 Single-Player C++ Shooter Project

> **Unreal Engine 5.5 기반의 팀 프로젝트 (5인 협업)** > **개발 기간:** 2025.07.29 - 2025.08.18 (3주)  
> **주요 역할:** AI 시스템 전반 설계 및 전투 로직 구현 (Enemy AI 파트)

---

## 🛠 Tech Stack
- **Engine:** Unreal Engine 5.5
- **Language:** C++
- **Framework:** Behavior Tree, AI Perception, Gameplay Tags
- **Architecture:** Component-Based Design, Interface-Driven

---

## 🤖 Core AI System (Enemy)
본 프로젝트에서는 확장성과 유지보수성을 극대화하기 위해 **컴포넌트 기반 아키텍처**를 설계하고, **Behavior Tree**를 활용한 지능형 전투 AI를 구현했습니다.

### 1. Component-Based Combat Architecture
공격 로직을 캐릭터 클래스에서 분리하여 재사용 가능한 컴포넌트 형태로 설계했습니다.
* **BaseAttackComponent:** 공격 상태 관리 및 공통 데이터를 처리하는 추상 클래스
* **Melee/RangedAttackComponent:** 근접/원거리 타입별 특화된 공격 행동(투사체 발사, 히트 체크 등) 구현
* **Benefit:** 새로운 적 타입 추가 시 코드 수정 없이 컴포넌트 조합만으로 기능 확장 가능

### 2. Intelligent Behavior Tree & Perception
`AI Perception`과 주기적 서비스(`BTService`)를 결합하여 정교한 상태 전이를 구현했습니다.
* **State Logic:** `Idle` → `Patrol` ↔ `Chase` ↔ `Attack` ↔ `Investigate`
* **Precision Targeting:** 캡슐 반지름을 고려한 **보정 거리(Adjusted Distance)** 계산을 통해 정확한 사거리 판정 및 공격 실행
* **Ranged AI Tactics:** 플레이어와의 최적 거리를 유지하며 좌우 회피 기동을 병행하는 전략적 AI 구현

### 3. Bullet Detection System (Reactive AI)
시야 밖에서 날아오는 총알을 감지하고 대응하는 반응형 시스템입니다.
* **Detection Logic:** 캐릭터 주변에 `BulletDetectionSphere`를 배치하여 비가시 영역의 탄환 감지
* **Automatic Tracking:** 감지 즉시 발사자의 위치를 블랙보드(`TargetLastKnownLocation`)에 갱신하여 즉각적인 추적 상태 전이

---

## 🚀 Troubleshooting & Optimization
### 1. 사망 애니메이션 및 액터 제거 동기화
- **Issue:** `SetLifeSpan`에 의한 액터 제거 시간과 사망 몽타주 재생 시간이 어긋나 모델이 Idle 포즈로 복귀하는 버그 발생
- **Solution:** 몽타주의 재생 길이를 목표 시간에 맞춰 동적으로 속도(`Speed`)를 계산하는 로직 적용

### 2. 다수 AI 동시 공격 시 타이머 충돌
- **Issue:** 여러 적이 동시에 공격할 때 전역 타이머 핸들 충돌로 애니메이션이 무효화되는 현상
- **Solution:** `TMap<TWeakObjectPtr<APawn>, FTimerHandle>` 구조를 도입하여 개체별 독립 타이머 생명 주기 관리

### 3. 디버그 시각화(Visualization) 시스템
- **Feature:** `bDrawDebug` 토글을 통해 시야 범위, 공격 반경, 추적선을 에디터 상에서 실시간 확인 가능하도록 구현하여 협업 및 테스트 효율성 증대
  
---

## 📺 Demonstration
- **유튜브 시연 영상:** [https://youtu.be/G-3N4tfBCYA?si=IqFxe5J2-N2-zwTf]
- **주요 포인트:** AI 상태 전이, 원거리 회피 기동, 총알 감지 및 추적 시스템
  
---

## 📂 AI Folder Structure
```text
Source/Room/AI/
├── Components/         # 추상화된 공격 컴포넌트 (Melee, Ranged)
├── Controllers/        # Perception 및 BT 제어 (EnemyAIController)
├── Tasks/              # BT Task (MaintainDistance, PerformAttack 등)
├── Services/           # 주기적 상태 검사 (CheckAttackRange)
└── Interface/          # 디커플링을 위한 AI 인터페이스
