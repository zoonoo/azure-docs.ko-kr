<properties
 pageTitle="미리 구성된 원격 모니터링 솔루션 연습 | Microsoft Azure"
 description="Azure IoT에 대한 설명은 원격 모니터링 솔루션 및 해당 아키텍처를 미리 구성합니다."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2015"
 ms.author="stevehob"/>

# 미리 구성된 원격 모니터링 솔루션 연습

## 소개

미리 구성된 IoT Suite 원격 모니터링 솔루션은 원격 위치에서 여러 컴퓨터를 작동하는 비즈니스 시나리오를 위한 기본 종단간 모니터링 솔루션입니다. 이 솔루션은 비즈니스 시나리오의 일반 구현을 제공하기 위한 키 Azure IoT Suite 서비스를 결합하며 자신의 특정 비즈니스 요구 사항을 충족시키기 위해 이러한 유형의 IoT 솔루션을 구현하려는 고객을 위한 시작 지점입니다.

## 논리 아키텍처

다음 다이어그램에서는 미리 구성된 솔루션의 논리적 구성 요소를 간략히 보여줍니다.

![](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


### 시뮬레이션된 장치

미리 구성된 솔루션에서 시뮬레이션된 장치가 냉각 장치(예: 건물 공조기 또는 시설 공기 처리 장치)를 나타냅니다. 시뮬레이션된 각 장치는 IoT Hub에 다음과 같은 원격 분석 메시지를 보냅니다.


| Message | 설명 |
|----------|-------------|
| 시작 | 장치가 시작되면, 장치 ID, 장치 메타데이터, 장치가 지원하는 명령의 목록, 장치의 현재 구성 등의 자체 정보를 포함한 **장치 정보** 메시지를 보냅니다. |


시뮬레이션된 장치는 다음과 같은 장치 속성을 메타 데이터로 보냅니다.

| 속성 | 목적 |
|------------------------|--------- |
| 장치 ID | 솔루션에서 장치를 만들 때 제공되거나 할당되는 ID입니다. |
| 제조업체 | 장치 제조업체 |
| 모델 번호 | 장치의 모델 번호 |
| 일련 번호 | 장치의 일련 번호 |
| 펌웨어 | 장치 펌웨어의 현재 버전 |
| 플랫폼 | 장치의 플랫폼 아키텍처 |
| 프로세서 | 장치를 실행하는 프로세서 |
| 설치된 RAM | 장치에 설치된 RAM의 양 |
| 허브 활성 상태 | 장치의 IoT Hub 상태 속성 |
| 만든 시간 | 솔루션에서 장치가 만들어진 시간 |
| 업데이트된 시간 | 장치에 대해 속성이 업데이트된 마지막 시간 |
| 위도 | 장치의 위도 위치 |
| 경도 | 장치의 경도 위치 |

시뮬레이터는 샘플 값으로 시뮬레이션된 장치에 이러한 속성을 시드합니다. 시뮬레이터가 시뮬레이션된 장치를 초기화할 때마다 장치는 IoT Hub에 미리 정의된 메타데이터를 게시합니다. 이는 장치 포털에서 만든 모든 메타데이터를 덮어쓰고 업데이트합니다.


시뮬레이션된 장치는 IoT Hub에서 보낸 다음과 같은 명령을 처리할 수 있습니다.

| 명령 | 설명 |
|------------------------|-----------------------------------------------------|
| PingDevice | 장치에 _ping_을 보내 활성 상태임을 확인합니다. |
| StartTelemetry | 장치의 원격 분석 발신을 시작합니다. |
| StopTelemetry | 장치의 원격 분석 발신을 차단합니다. |
| ChangeSetPointTemp | 임의 데이터가 생성되는 지점 주위의 설정 지점 값을 변경합니다. |
| DiagnosticTelemetry | 추가 원격 분석 값(externalTemp)을 보낼 장치 시뮬레이터를 트리거합니다. |
| ChangeDeviceState | 장치의 확장된 상태 속성을 변경하고 장치로부터 받은 장치 정보 메시지를 전송합니다. |


장치 명령 승인은 IoT Hub를 통해 제공됩니다.


### Azure 스트림 분석 작업

**작업 1: 원격 분석**은 두 가지 명령을 사용하여 들어오는 장치 원격 분석 스트림에서 작동합니다. 첫 번째 명령은 모든 원격 분석 메시지를 장치에서 영구 Blob 저장소로 보냅니다. 두 번째 명령은 5분짜리 슬라이딩 윈도우를 통해 평균, 최소 및 최대 습도 값을 계산합니다. 이 데이터는 Blob 저장소에도 보내집니다.

**작업 2: 장치 정보**는 들어오는 메시지 스트림에서 장치 정보 메시지를 필터링하고 이벤트 허브 끝점으로 보냅니다. 장치는 시작 시 그리고 **SendDeviceInfo** 명령에 반응하여 장치 정보 메시지를 보냅니다.

**작업 3: 규칙**은 장치 단위 임계값에 대해 들어오는 온도 및 습도 원격 분석 값을 평가합니다. 임계값은 솔루션에 포함된 규칙 편집기에서 설정됩니다. 각 장치/값 쌍은 **참조 데이터**로서 스트림 분석에서 읽는 Blob의 타임스탬프에 의해 저장됩니다. 작업은 장치에 대해 설정한 임계값에 대해 비지 않은 값을 비교합니다. ' >' 조건을 초과하면, 작업은 **경보** 이벤트를 출력하여 임계값을 초과했음을 나타내고 장치, 값 및 타임스탬프 값을 제공합니다.

### 이벤트 프로세서

**이벤트 프로세서**는 장치 정보 메시지와 명령 응답을 처리합니다. 이는 다음을 사용합니다.

- 현재 장치 정보로 장치 레지스트리(DocumentDB 데이터베이스에 저장됨)를 업데이트할 장치 정보 메시지
- 장치 명령 기록(DocumentDB 데이터베이스에 저장됨)을 업데이트하는 명령 응답 메시지

## 탐색을 시작하겠습니다.

이 섹션은 솔루션의 구성 요소를 안내하고, 의도된 사용 사례에 설명하며, 예제를 제공합니다.

### 원격 모니터링 대시보드
웹 응용 프로그램의 이 페이지는 PowerBI javascript 컨트롤([PowerBI-시각 리포지토리](https://www.github.com/Microsoft/PowerBI-visuals) 참조)을 사용하여 Blob 저장소에서 스트림 분석 작업의 출력 데이터를 시각화합니다.


### 장치 관리 포털

이 웹 앱을 통해 다음을 수행할 수 있습니다.

- 고유 장치 ID를 설정하고 인증 키를 생성하는 새 장치를 프로비전합니다.
- 기존 속성 보기 및 새 속성으로 업데이트를 포함하는 장치 속성을 관리합니다.
- 명령을 장치로 보냅니다.
- 장치의 명령 기록을 봅니다.

### 클라우드 솔루션의 동작 관찰
[Azure 포털](https://portal.azure.com)로 가고 지정한 솔루션 이름으로 리소스 그룹으로 이동함으로써 프로비전된 리소스를 볼 수 있습니다.

![](media/iot-suite-remote-monitoring-sample-walkthrough/azureportal_01.png)

우선 샘플을 실행하면, 4개의 미리 구성된 시뮬레이션된 장치가 있습니다.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_01.png)

시뮬레이션된 새 장치를 추가하기 위해 장치 관리 포털을 사용할 수 있습니다.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_02.png)

처음에는 장치 관리 포털의 새 장치 상태는 **보류 중**입니다.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_03.png)

앱이 시뮬레이션된 장치 배포 작업을 완료하면, 다음 스크린샷처럼 장치 관리 포털에서 장치 상태가 **실행**으로 변경되는 것을 볼 수 있습니다. **DeviceInfo** 스트림 분석 작업은 장치에서 장치 관리 포털로 장치 상태 정보를 보냅니다.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_04.png)

솔루션 포털을 사용하여 **ChangeSetPointTemp**와 같은 명령을 다음과 같은 장치에 보낼 수 있습니다.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_05.png)

장치가 명령을 성공적으로 실행했다고 보고하면, 상태는 **성공**으로 변경됩니다.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_06.png)

솔루션 포털을 사용하여 모델 번호와 같은 특정 특성을 가진 장치를 검색할 수 있습니다.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_07.png)

장치를 비활성화할 수 있으며, 비활성화된 후에는 파일을 제거할 수 있습니다.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_08.png)

<!---HONumber=AcomDC_0218_2016-->