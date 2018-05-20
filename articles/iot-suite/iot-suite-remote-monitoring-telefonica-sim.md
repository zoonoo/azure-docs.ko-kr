---
title: 원격 모니터링 솔루션에 SIM 데이터 통합 - Azure| Microsoft Docs
description: 이 문서에서는 Telefonica SIM 데이터를 원격 모니터링 솔루션에 통합하는 방법을 제공합니다.
services: iot-suite
suite: iot-suite
author: hegate
manager: corywink
ms.author: hegate
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c82bb8ff3d0f903e1de627f13337ae5fc633458c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>원격 모니터링 솔루션에 SIM 데이터 통합

## <a name="overview"></a>개요
IoT 장치는 어디에서나 데이터 스트림을 보낼 수 있는 SIM 카드를 사용하여 클라우드에 연결하는 경우가 많습니다. Azure IoT 원격 모니터링 솔루션은 SIM 관리 데이터의 통합이 가능하기 때문에 SIM에서 제공되는 데이터를 통해 장치 상태를 추적할 수도 있습니다. 원격 모니터링은 Telefonica IoT와의 통합을 기본 제공하기 때문에 해당사의 IoT 연결 플랫폼을 사용하는 고객은 자신의 장치 SIM 연결 데이터와 솔루션을 동기화 할 수 있습니다. 이 솔루션은 GitHub 리포지토리를 통해 다른 전화 회사 공급자를 지원하도록 확장될 수 있습니다.
이 자습서에서는 다음 방법에 대해 알아봅니다.
* SIM 데이터를 원격 모니터링 솔루션에 통합
* 실시간 원격 분석 보기
* SIM 데이터 보기 

## <a name="telefonica-iot-integration-setup"></a>Telefonica IoT 통합 설정

### <a name="prerequisites"></a>필수 조건
연결 데이터를 Azure 원격 모니터링 솔루션에 동기화하려면 다음 단계를 따르십시오.

1.  [Telefonica 사이트](https://iot.telefonica.com/contact)에서 요청을 입력하고 연락처 데이터를 포함하여 **Azure 원격 모니터링** 옵션을 선택합니다.
2.  Telefonica에서 귀하의 계정이 활성화됩니다. 
3.  Telefónica 고객이 아닌 경우 이 서비스나 다른 IoT 연결 클라우드 지원 서비스를 사용하려면 [Telefonica 사이트](https://iot.telefonica.com/contact)로 이동하여 **연결** 옵션을 선택합니다.

### <a name="telefonica-sim-setup"></a>Telefonica SIM 설정
Telefónica SIM과 Azure Twin 장치 ID 연결은 Telefónica IoT SIM "별칭" 속성을 기반으로 합니다. 

[Telefónica IoT Connectivity Platform 포털](https://m2m-movistar-es.telefonica.com/) > SIM 인벤토리로 이동하여 SIM을 선택하고 각 SIM "별칭"을 원하는 Twin deviceID로 업데이트합니다. 

이 작업은 대량 모드로도 수행할 수 있습니다(Telefónica IoT Connectivity Platform 사용자 설명서 참조).

![Telefonica 업데이트](media/iot-suite-remote-monitoring-telefonica/telefonica_site.png)

장치를 원격 모니터링에 연결하려면, [C](iot-suite-connecting-devices-linux.md) 또는 [Node](iot-suite-connecting-devices-node.md)를 사용하여 자습서를 수행합니다. 

## <a name="view-device-telemetry-and-sim-properties"></a>장치 원격 분석 및 SIM 속성 보기
Telefonica 계정이 올바르게 구성되고 장치가 연결되면 장치 정보와 SIM 데이터를 볼 수 있습니다.
다음 연결 매개 변수를 게시할 수 있습니다.
* ICCID
* IP
* 현재 네트워크 상태
* SIM 상태
* 네트워크 기반 위치
* 사용된 데이터 트래픽

![대시보드](media/iot-suite-remote-monitoring-telefonica/dashboard.png)
 
## <a name="next-steps"></a>다음 단계

SIM 데이터를 Azure IoT 원격 모니터링에 통합하는 방법에 대한 개요를 알아보았습니다. 아래는 솔루션 가속화를 위해 제안되는 다음 단계입니다.

* [Azure IoT 원격 모니터링 솔루션 운영](iot-suite-remote-monitoring-explore.md)
* [고급 모니터링 수행](iot-suite-remote-monitoring-monitor.md)
* [장치 관리](iot-suite-remote-monitoring-manage.md)
* [장치 문제 해결](iot-suite-remote-monitoring-maintain.md)

