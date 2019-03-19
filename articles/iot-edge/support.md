---
title: 지원되는 운영 체제, 컨테이너 엔진 - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge 디먼 및 런타임을 실행할 수 있는 운영 체제와 프로덕션 디바이스의 지원되는 컨테이너 엔진에 대해 알아봅니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5c215c125237d72875155f15002fca6c4bee53b1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077737"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge 지원 시스템

Azure IoT Edge 제품에 대한 지원을 찾는 다양한 방법이 있습니다.

**버그 보고** – Azure IoT Edge 제품에 대한 대다수의 개발은 IoT Edge 오픈 소스 제품에서 이루어집니다. 버그는 프로젝트의 [문제 페이지](https://github.com/azure/iotedge/issues)에서 보고됩니다. 프로젝트에서 제품 업데이트로 신속하게 문제를 해결합니다.

**Microsoft 고객 지원팀** – [지원 계획](https://azure.microsoft.com/support/plans/)이 있는 사용자는 [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)에서 직접 지원 티켓을 만들어서 Microsoft 고객 지원팀과 협력할 수 있습니다.

**기능 요청** – Azure IoT Edge 제품은 제품의 [사용자 의견 페이지](https://feedback.azure.com/forums/907045-azure-iot-edge)를 통해 기능 요청을 추적합니다.

## <a name="container-engines"></a>컨테이너 엔진
Azure IoT Edge는 컨테이너로 구현되므로 모듈을 시작할 컨테이너 엔진이 필요합니다. Microsoft에서는 컨테이너 엔진인 Moby 엔진을 제공하여 이러한 요구 사항을 충족합니다. 해당 기능은 Moby 오픈 소스 프로젝트에 기반합니다. Docker CE 및 Docker EE도 널리 사용되는 다른 컨테이너 엔진이며, Moby 오픈 소스 프로젝트를 기반으로 하고 Azure IoT Edge와 호환됩니다. Microsoft에서는 이러한 컨테이너 엔진을 사용하는 시스템에 대해 최고의 지원을 제공하지만 해당 시스템에서 발생한 문제를 수정하는 기능은 제공하지 않습니다. 이로 인해 Microsoft에서는 프로덕션 시스템에서 Moby 엔진을 사용하는 것이 좋습니다.

<br>
<center>

![컨테이너 런타임을 모 비](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>운영 체제
Azure IoT Edge는 컨테이너를 실행할 수 있는 대부분의 운영 체제에서 실행되지만 모두가 동일하게 지원되지는 않습니다. 운영 체제는 사용자가 사용할 수 있는 지원 수준을 나타내는 계층으로 그룹화됩니다.
* 계층 1 시스템은 공식적으로 지원된다고 간주할 수 있습니다. 즉, Microsoft에서는
    * 자동화된 테스트에서 이 운영 체제를 사용하고
    * 해당 운영 체제에 대한 설치 패키지를 제공합니다.
* 계층 2 시스템은 Azure IoT Edge와 호환 가능하다고 간주할 수 있고 상대적으로 쉽게 사용할 수 있습니다. 이는 다음을 의미합니다.
    * Microsoft은 플랫폼에서 임시 테스트를 수행한 또는 플랫폼에서 Azure IoT Edge를 성공적으로 실행 하는 파트너의 인식
    * 다른 플랫폼에 대한 설치 패키지는 다음 플랫폼에서 작동할 수 있습니다.
    
호스트 OS 제품군은 항상 모듈의 컨테이너 내부에서 사용되는 게스트 OS 제품군과 일치해야 합니다. 즉, Linux에서는 Linux 컨테이너만 사용하고 Windows에서는 Windows 컨테이너만 사용할 수 있습니다. Windows를 사용하는 경우 프로세스 격리 컨테이너만 지원되고 hyper-v 격리 컨테이너는 지원되지 않습니다.  

<br>
<center>

![호스트 OS와 게스트 OS 일치](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>계층 1
일반 공급

| 운영 체제 | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Raspbian-stretch | 아니요 | 예.|
| Ubuntu Server 16.04 | 예 | 아니요 |
| Ubuntu Server 18.04 | 예 | 아니요 |

공개 미리 보기

| 운영 체제 | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core 빌드 17763 | 예 | 아니요 |
| Windows 10 IoT Enterprise 빌드 17763 | 예 | 아니요 |
| Windows Server 2019 | 예 | 아니요 |

위의 Windows OS는 Windows에서 Windows 컨테이너를 실행하는 디바이스의 요구 사항입니다. 이것이 프로덕션에서 지원되는 유일한 구성입니다. Windows용 Azure IoT Edge 설치 패키지를 통해 Windows에서 Linux 컨테이너를 사용할 수 있지만 개발 및 테스트 전용입니다. Windows에서 Linux 컨테이너를 사용하는 것은 프로덕션에서 지원되는 구성이 아닙니다. 이 개발 시나리오에서는 Windows 10 빌드 14393 이상 및 Windows Server 2016 이상의 임의 버전을 사용할 수 있습니다.

### <a name="tier-2"></a>계층 2

| 운영 체제 | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | 예 | 예 |
| Debian 8 | 예 | 예 |
| Debian 9 | 예 | 예. |
| RHEL 7.5 | 예 | 예 |
| Ubuntu 18.04 | 예 | 예 |
| Ubuntu 16.04 | 예. | 예 |
| Wind River 8 | 예 | 아니요 |
| Yocto | 예 | 아니요 |


## <a name="virtual-machines"></a>Virtual Machines
가상 머신에서 Azure IoT Edge를 실행할 수 있습니다. 일반적으로 고객이 에지 인텔리전스를 사용하여 기존 인프라를 보강하려는 경우에 사용됩니다. 호스트 VM OS 제품군은 모듈의 컨테이너 내부에서 사용되는 게스트 OS 제품군과 일치해야 합니다. 이는 Azure IoT Edge가 디바이스에서 직접 실행될 때와 동일한 요구 사항입니다. Azure IoT Edge는 기본 가상화 기술에 중립적이며, Hyper-V 및 vSphere와 같은 플랫폼을 통해 구동되는 VM에서 작동합니다.

<br>
<center>

![VM에서 azure IoT Edge](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>최소 시스템 요구 사항
Azure IoT Edge는 Raspberry Pi3만큼 작은 디바이스부터 서버 등급 하드웨어까지 잘 실행됩니다. 시나리오에 적합한 하드웨어 선택은 실행하려는 워크로드에 따라 다릅니다. 최종 디바이스 결정은 복잡할 수 있습니다. 그러나 기존 노트북이나 데스크톱에서 쉽게 솔루션 프로토타입 생성을 시작할 수 있습니다.

프로토타입 생성 경험은 최종 디바이스 선택에 도움이 됩니다. 고려해야 하는 질문에는 워크로드를 구성하는 모듈 수, 모듈의 컨테이너가 공유하는 계층 수, 모듈이 작성된 언어, 모듈에서 처리할 데이터의 양, 모듈의 워크로드 액세스에 특수 하드웨어가 필요한지 여부, 원하는 솔루션의 성능 특성, 하드웨어 예산 등이 포함됩니다.
