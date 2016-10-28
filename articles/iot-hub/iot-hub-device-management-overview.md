<properties
 pageTitle="장치 관리 개요 | Microsoft Azure"
 description="Azure IoT Hub 장치 관리의 개요"
 services="iot-hub"
 documentationCenter=""
 authors="bzurcher"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/16/2016"
 ms.author="bzurcher"/>



# Azure IoT Hub 장치 관리의 개요(미리 보기)

## Azure IoT 장치 관리 방식

Azure IoT Hub 장치 관리는 IoT의 다양한 장치 및 프로토콜에 대해 IoT 장치 관리를 활용하도록 장치 및 백 엔드에 대한 기능 및 확장 모델을 제공합니다. IoT의 장치는 매우 제한된 센서, 단일 용도 마이크로컨트롤러에서 다른 장비 및 프로토콜을 가능하게 하는 보다 강력한 게이트웨이에 이릅니다. IoT 솔루션은 각 도메인의 운영자에 대한 고유한 사용 사례에 따라서 Vertical 도메인 및 응용 프로그램이 상당히 다양합니다. IoT 솔루션은 광범위한 장치 및 사용자에 대한 장치 관리가 가능하도록 IoT Hub 장치 관리 기능, 패턴 및 코드 라이브러리를 활용할 수 있습니다.

## 소개

성공적인 IoT 솔루션을 만드는 결정적인 부분은 운영자가 다수의 장치에 대한 지속적인 관리를 처리하는 방법에 대한 전략을 제공하는 것입니다. IoT 운영자는 간단하고 안정적이면서 업무 중 보다 전략적인 측면에 중점을 둘 수 있도록 해 주는 도구와 응용 프로그램을 필요로 합니다. Azure IoT Hub는 가장 중요한 장치 관리 패턴을 조성하는 IoT 응용 프로그램을 만들기 위한 구성 요소를 제공합니다.

장치를 클라우드에 안전하게 연결하는 장치 관리 에이전트라고 하는 간단한 응용 프로그램을 실행하는 경우 장치가 IoT Hub에 의해 관리되는 것으로 간주됩니다. 에이전트 코드는 응용 프로그램 쪽 운영자가 원격으로 장치 상태를 입증하고 네트워크 구성 변경을 적용하거나 펌웨어 업데이트를 배포하는 등의 관리 작업을 수행할 수 있도록 합니다.

## IoT 장치 관리 원칙

IoT는 특유의 관리 과제를 수반하며 솔루션은 다음과 같은 IoT 장치 관리 원칙을 처리해야 합니다.

![][img-dm_principles]

- **규모 및 자동화**: IoT는 일상적인 작업을 자동화할 수 있는 간단한 도구를 필요로 하며 비교적 적은 수의 운영 담당자가 수백만 대의 장치를 관리할 수 있도록 해 줍니다. 운영자는 매일 원격에서 일괄적으로 장치 작업을 처리하고 운영자가 주의를 기울여야 하는 문제가 발생할 때만 경고를 받게 됩니다.

- **개방성 및 호환성**: IoT 장치 생태계는 매우 다양합니다. 관리 도구는 다수의 장치 클래스, 플랫폼 및 프로토콜을 수용하도록 조정되어야 합니다. 운영자는 가장 제한된 내장형 단일 프로세스 칩에서 강력하고 완벽하게 작동하는 컴퓨터에 이르는 모든 장치를 지원할 수 있어야 합니다.

- **컨텍스트 인식**: IoT 환경은 동적이며 변화무쌍합니다. 서비스의 안정성이 다른 무엇보다 가장 중요합니다. 장치 관리 작업은 유지 관리 중단 시간이 중대한 비즈니스 작업에 영향을 미치거나 위험한 상황을 만들지 않도록 SLA 유지 관리 기간, 네트워크 및 전력 상태, 사용 중인 조건, 장치의 지리적 위치를 감안해야 합니다.

- **많은 역할 서비스**: IoT 운영 역할 특유의 워크플로와 프로세스에 대한 지원이 중요합니다. 운영 담당자는 내부 IT 부서에 주어진 제약 조건에 맞도록 작업을 진행해야 하며, 적절할 장치 운영 정보를 감독자 및 기타 관리 역할 담당자에게 표면화시켜야 합니다.

## IoT 장치 수명 주기 

IoT 프로젝트는 매우 다양하지만 장치를 관리하는 일반적인 패턴이 있습니다. Azure IoT에서는 이러한 패턴이 다섯 가지 뚜렷한 단계로 구성된 IoT 장치 수명 주기 내에서 식별됩니다.

![][img-device_lifecycle]

1. **계획**: 운영자가 대량 관리 작업을 위해 장치 그룹을 간편하고 정확하게 대상화하고 쿼리하기 위한 장치 속성 구성표를 만들 수 있습니다.

    *관련된 구성 요소*: [쌍 장치 시작][lnk-twins-getstarted], [쌍 속성 사용 방법][lnk-twin-properties]

2. **프로비전**: 새 장치를 IoT Hub에 안전하게 인증하고 운영자가 장치의 기능과 현재 상태를 즉시 검색할 수 있도록 합니다.

    *관련된 구성 요소*: [IoT Hub 시작][lnk-hub-getstarted], [쌍 속성 사용 방법][lnk-twin-properties]

3. **구성**: 정상적인 상태와 보안을 유지하면서 장치에 대한 일괄 구성 변경 및 펌웨어 업데이트를 가능하게 합니다.

    *관련된 구성 요소*: [쌍 속성 사용 방법][lnk-twin-properties], [C2D 메서드][lnk-c2d-methods], [작업 예약/브로드캐스트][lnk-jobs]

4. **모니터링**: 주의가 필요한 문제를 운영자에게 알려주기 위하여 전반적인 장치들의 상태와 지속적인 업데이트 롤아웃 상태를 모니터링합니다.

    *관련된 구성 요소*: [쌍 속성 사용 방법][lnk-twin-properties]

5. **사용 중지**: 오류가 발생하거나, 업그레이드 주기 후에 또는 서비스 수명 주기가 끝나면 장치를 교체하거나 서비스를 해제합니다.

    *관련된 구성 요소*:
    
## IoT Hub 장치 관리 패턴

IoT Hub는 다음과 같은 (초기) 장치 관리 패턴을 가능하게 합니다. [자습서][lnk-get-started]의 내용처럼, 이러한 패턴을 시나리오에 꼭 맞게 확장할 수 있으며 코어 패턴을 기반으로 다른 시나리오를 위한 새로운 패턴을 디자인할 수 있습니다.

1. **다시 부팅** - 백 엔드 응용 프로그램은 D2C 메서드를 통해 재부팅이 시작된 것을 장치에 알립니다. 장치는 장치 쌍 보고 속성을 사용하여 장치의 재부팅 상태를 업데이트합니다.

    ![][img-reboot_pattern]

2. **공장 재설정** - 백 엔드 응용 프로그램은 D2C 메서드를 통해 공장 재설정이 시작된 것을 장치에 알립니다. 장치는 장치 쌍 보고 속성을 사용하여 장치의 공장 재설정 상태를 업데이트합니다.

    ![][img-facreset_pattern]

3. **구성** - 백 엔드 응용 프로그램은 장치 쌍에 필요한 속성을 사용하여 장치에서 실행 중인 소프트웨어를 구성합니다. 장치는 장치 쌍 보고 속성을 사용하여 장치의 구성 상태를 업데이트합니다.

    ![][img-config_pattern]

4. **펌웨어 업데이트** - 백 엔드 응용 프로그램은 D2C 메서드를 통해 펌웨어 업데이트가 시작된 것을 장치에 알립니다. 장치는 펌웨어 패키지를 다운로드하는 다단계 프로세스를 시작하고, 펌웨어 패키지를 적용하고, 마지막으로 IoT Hub 서비스에 다시 연결합니다. 다단계 프로세스가 진행되는 동안, 장치는 장치 쌍 보고 속성을 사용하여 진행률과 장치의 상태를 업데이트합니다.

    ![][img-fwupdate_pattern]

5. **진행률 및 상태 보고** - 응용 프로그램 백 엔드는 장치에서 실행 중인 작업의 상태와 진행률을 보고하기 위하여 일련의 장치 전반에 대해 장치 쌍 쿼리를 실행합니다.

    ![][img-report_progress_pattern]

## 다음 단계

개발자는 Azure IoT Hub에 제공되는 구성 요소를 사용하여 장치 수명 주기의 각 단계에서 IoT 운영자 특유의 요구 사항을 충족시키는 IoT 응용 프로그램을 만들 수 있습니다.

Azure IoT Hub 장치 관리 기능에 대해 계속 알아보려면 [Azure IoT Hub 장치 관리 시작][lnk-get-started] 자습서를 참조하세요.

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md

<!---HONumber=AcomDC_1005_2016-->