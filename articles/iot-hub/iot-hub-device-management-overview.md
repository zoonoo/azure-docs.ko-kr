---
title: Azure IoT Hub 디바이스 관리 개요 | Microsoft Docs
description: 'Azure IoT Hub의 디바이스 관리에 대한 개요: 엔터프라이즈 디바이스 수명 주기 및 재부팅, 공장 재설정, 펌웨어 업데이트, 구성, 디바이스 배, 쿼리, 작업과 같은 디바이스 관리 패턴.'
author: bzurcher
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.openlocfilehash: bdc55af23568b5785a831e81f352400c728c902e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60400991"
---
# <a name="overview-of-device-management-with-iot-hub"></a>IoT Hub를 사용한 디바이스 관리 개요

Azure IoT Hub는 디바이스 및 백 엔드 개발자가 강력한 디바이스 관리 솔루션을 빌드할 수 있도록 하는 기능 및 확장성 모델을 제공합니다. 디바이스의 범위는 제한된 센서 및 단일 목적 마이크로컨트롤러에서 다수의 디바이스에 대한 통신을 라우팅하는 강력한 게이트웨이까지를 포함합니다.  또한 IoT 운영자의 사용 사례 및 요구 사항은 여러 산업에 따라 크게 다릅니다.  IoT Hub 디바이스 관리는 각기 다른 요구 사항을 충족하면서도 기능, 패턴 및 코드 라이브러리를 다양한 디바이스 및 최종 사용자에게 제공합니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

성공적인 기업 IoT 솔루션을 만드는 데 있어 중요한 부분은 운영자가 다수의 디바이스를 지속적으로 관리하는 방법에 대한 전략을 제공하는 것입니다. IoT 운영자는 업무 중 보다 전략적인 측면에 중점을 둘 수 있는 간단하고 안정적인 도구와 애플리케이션을 필요로 합니다. 이 문서는 다음을 제공합니다.

* 디바이스 관리에 대한 Azure IoT Hub 접근 방식의 간략한 개요
* 일반적인 디바이스 관리 원칙에 대한 설명
* 디바이스 수명 주기에 대한 설명
* 일반적인 디바이스 관리 패턴의 개요

## <a name="device-management-principles"></a>디바이스 관리 원칙

IoT는 특유의 디바이스 관리 과제를 수반하며 모든 기업 수준의 솔루션은 다음과 같은 원칙을 처리해야 합니다.

![디바이스 관리 원칙 그래픽](media/iot-hub-device-management-overview/image4.png)

* **크기 조정 및 자동화**: IoT 솔루션의 일상적인 작업을 자동화 하는 상대적으로 적은 운영 담당자가 수백만 대의 장치 관리를 사용 하도록 설정 하는 간단한 도구를 필요로 합니다. 운영자는 매일 원격에서 일괄적으로 디바이스 작업을 처리하고 운영자가 주의를 기울여야 하는 문제가 발생할 때만 경고를 받게 됩니다.

* **개방성 및 호환성**: 장치 생태계는 매우 다양 합니다. 관리 도구는 다수의 디바이스 클래스, 플랫폼 및 프로토콜을 수용하도록 조정되어야 합니다. 운영자는 가장 제한된 내장형 단일 프로세스 칩에서 강력하고 완벽하게 작동하는 컴퓨터에 이르는 많은 종류의 디바이스를 지원할 수 있어야 합니다.

* **컨텍스트 인식**: IoT 환경은 동적 이며 변화무쌍 합니다. 서비스 안정성이 다른 무엇보다 가장 중요합니다. 디바이스 관리 작업은 유지 관리 중단 시간이 중대한 비즈니스 작업에 영향을 미치거나 위험한 상황을 만들지 않도록 다음 요소를 감안해야 합니다.

    * SLA 유지 관리 기간
    * 네트워크 및 전력 상태
    * 사용 중인 조건
    * 디바이스의 지리적 위치

* **많은 역할 서비스**: 고유 워크플로 및 IoT 운영 역할 프로세스에 대 한 지원이 매우 중요 합니다. 운영 담당자는 내부 IT 부서에 주어진 제약 조건에 맞도록 작업을 진행해야 합니다.  또한 실시간 디바이스 운영 정보를 감독자 및 기타 관리 역할 담당자에게 표면화시키는 지속 가능한 방법을 찾아내야 합니다.

## <a name="device-lifecycle"></a>디바이스 수명 주기
모든 기업 IoT 프로젝트에 공통된 일련의 일반 디바이스 관리 단계가 있습니다. Azure IoT의 디바이스 수명 주기 내에는 5단계가 있습니다.

![5개의 Azure IoT 디바이스 수명 주기 단계는 계획, 구축, 구성, 모니터링, 사용 중지입니다.](./media/iot-hub-device-management-overview/image5.png)

다섯 단계 각각에서 완벽한 솔루션을 제공하기 위해 충족해야 하는 여러 가지 디바이스 연산자 요구 사항이 있습니다.

* **계획**: 쉽게 수 있게 해 주는 장치 메타 데이터 구성표를 만들려면 연산자를 사용 하도록 설정 하 고 정확 하 게 쿼리 하기 위한 장치 대량 관리 작업에 대 한 그룹을 대상으로 합니다. 디바이스 쌍을 사용하여 이 디바이스 메타데이터를 태그 및 속성 형식으로 저장할 수 있습니다.
  
    *추가 참고 자료*: 
    * [쌍 디바이스 시작](iot-hub-node-node-twin-getstarted.md)
    * [쌍 디바이스 이해](iot-hub-devguide-device-twins.md)
    * [디바이스 쌍 속성을 사용하는 방법](tutorial-device-twins.md)
    * [IoT 솔루션 내에서 디바이스 구성에 대한 모범 사례](iot-hub-configuration-best-practices.md)

* **프로 비전**: 안전 하 게 새 장치를 IoT Hub를 프로 비전 하 고 장치 기능을 즉시 검색할 연산자를 사용 하도록 설정 합니다.  IoT Hub ID 레지스트리를 사용하여 유연한 디바이스 ID 및 자격 증명을 만들고 작업을 사용하여 대량으로 이 작업을 수행합니다. 디바이스 쌍에서 디바이스 속성을 통해 기능 및 조건을 보고하도록 디바이스를 빌드합니다.
  
    *추가 참고 자료*: 
    * [디바이스 ID 관리](iot-hub-devguide-identity-registry.md)
    * [장치 ID의 대량 관리](iot-hub-bulk-identity-mgmt.md)
    * [디바이스 쌍 속성을 사용하는 방법](tutorial-device-twins.md)
    * [IoT 솔루션 내에서 장치 구성에 대한 모범 사례](iot-hub-configuration-best-practices.md)
    * [Azure IoT Hub Device Provisioning Service](https://azure.microsoft.com/documentation/services/iot-dps)

* **구성**: 대량 구성 변경 및 장치에 펌웨어 업데이트를 용이 하 게 상태와 보안을 유지 하면서 합니다. 원하는 속성 또는 직접 메서드와 브로드캐스트 작업을 사용하여 대량으로 이러한 디바이스 관리 작업을 수행합니다.
  
    *추가 참고 자료*:
    * [디바이스 쌍 속성을 사용하는 방법](tutorial-device-twins.md)
    * [대규모로 IoT 장치 구성 및 모니터링](iot-hub-auto-device-config.md)
    * [IoT 솔루션 내에서 디바이스 구성에 대한 모범 사례](iot-hub-configuration-best-practices.md)

* **모니터**: 장치 컬렉션의 전체 상태, 진행 중인 작업 및 경고는 주의가 필요한 문제 운영자의 상태를 모니터링 합니다.  디바이스 쌍을 적용하여 디바이스 업데이트 작업의 상태를 실시간 운영 상태를 보고할 수 있도록 허용합니다. 디바이스 쌍 쿼리를 사용하여 즉각적인 문제를 노출하는 강력한 대시보드 보고서를 작성합니다.
  
    *추가 참고 자료*: 
    * [디바이스 쌍 속성을 사용하는 방법](tutorial-device-twins.md)
    * [디바이스 쌍, 작업 및 메시지 라우팅에 대한 IoT Hub 쿼리 언어](iot-hub-devguide-query-language.md)
    * [대규모로 IoT 장치 구성 및 모니터링](iot-hub-auto-device-config.md)
    * [IoT 솔루션 내에서 디바이스 구성에 대한 모범 사례](iot-hub-configuration-best-practices.md)

* **사용 중지**: 오류가 발생 한 후 장치를 서비스 해제를 주기, 업그레이드 또는 교체 하거나 서비스 수명 끝에 있습니다.  디바이스 쌍을 사용하여 물리적 디바이스를 바꾸는 경우 디바이스 정보를 유지하거나 사용이 중지될 경우 보관합니다. IoT Hub ID 레지스트리를 사용하여 디바이스 ID 및 자격 증명을 안전하게 해지합니다.
  
    *추가 참고 자료*: 
    * [디바이스 쌍 속성을 사용하는 방법](tutorial-device-twins.md)
    * [디바이스 ID 관리](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>디바이스 관리 패턴

IoT Hub는 다음과 같은 디바이스 관리 패턴을 가능하게 합니다. [디바이스 관리 자습서](iot-hub-node-node-device-management-get-started.md)에서는 정확한 시나리오에 맞도록 이러한 패턴을 확장하는 방법 및 이러한 핵심 템플릿을 기반으로 새 패턴을 디자인하는 방법을 자세히 알아보겠습니다.

* **다시 부팅**: 백 엔드 앱을 통해 장치에 알립니다 직접 메서드는 다시 부팅이 시작 된 것입니다.  디바이스는 보고된 속성을 사용하여 디바이스의 재부팅 상태를 업데이트합니다.
  
    ![디바이스 관리 다시 부팅 패턴 그래픽](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **공장 재설정**: 백 엔드 앱을 장치에 알립니다 직접 메서드를 통해는 공장 재설정이 시작 된 것입니다. 디바이스는 보고된 속성을 사용하여 디바이스의 공장 재설정 상태를 업데이트합니다.
  
    ![디바이스 관리 공장 재설정 패턴 그래픽](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **구성**: 백 엔드 앱에 장치에서 실행 되는 소프트웨어를 구성 하는 desired 속성을 사용 합니다. 디바이스는 보고된 속성을 사용하여 디바이스의 구성 상태를 업데이트합니다.
  
    ![디바이스 관리 구성 패턴 그래픽](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **펌웨어 업데이트**: 백 엔드 앱 자동 장치 관리 구성을 사용 하 여 장치 업데이트를 검색 하 고 업데이트 프로세스를 모니터링 하는 위치를 확인 하 고 업데이트를 받을 장치를 선택 합니다. 디바이스는 펌웨어 이미지를 다운로드, 확인 및 적용한 다음, IoT Hub 서비스에 다시 연결하기 전에 디바이스를 다시 부팅하는 다단계 프로세스를 시작합니다. 다단계 프로세스가 진행되는 동안, 디바이스는 보고된 속성을 사용하여 진행률과 디바이스의 상태를 업데이트합니다.
  
    ![디바이스 관리 펌웨어 업데이트 패턴 그래픽](media/iot-hub-device-management-overview/fwupdate-pattern.png)

* **진행률 및 상태 보고**: 솔루션 상태 및 장치에서 실행 중인 작업의 진행률을 보고 하려면 장치의 여러 최종 실행 장치 쌍 쿼리를 백업 합니다.
  
    ![디바이스 관리 보고 진행률 및 상태 패턴 그래픽](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="next-steps"></a>다음 단계

IoT Hub에서 디바이스 관리를 위해 제공하는 기능, 패턴 및 코드 라이브러리를 사용하면 각 디바이스 수명 주기 단계에서 기업 IoT 운영자 요구 사항을 충족하는 IoT 애플리케이션을 만들 수 있습니다.

IoT Hub 디바이스 관리 기능에 대해 계속 알아보려면 [디바이스 관리 시작](iot-hub-node-node-device-management-get-started.md) 자습서를 참조하세요.