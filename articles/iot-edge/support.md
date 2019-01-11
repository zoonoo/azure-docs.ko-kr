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
ms.openlocfilehash: 6443260de0a8bd8531edb303fa581d281034fef3
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555611"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge 지원 시스템

Azure IoT Edge 제품에 대한 지원을 찾는 다양한 방법이 있습니다.

**버그 보고** – Azure IoT Edge 제품에 대한 대다수의 개발은 IoT Edge 오픈 소스 제품에서 이루어집니다. 버그는 프로젝트의 [문제 페이지](https://github.com/azure/iotedge/issues)에서 보고됩니다. 프로젝트에서 제품 업데이트로 신속하게 문제를 해결합니다.

**Microsoft 고객 지원팀** – [지원 계획](https://azure.microsoft.com/support/plans/)이 있는 사용자는 [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)에서 직접 지원 티켓을 만들어서 Microsoft 고객 지원팀과 협력할 수 있습니다.

**기능 요청** – Azure IoT Edge 제품은 제품의 [사용자 의견 페이지](https://feedback.azure.com/forums/907045-azure-iot-edge)를 통해 기능 요청을 추적합니다.

## <a name="operating-systems"></a>운영 체제
Azure IoT Edge는 컨테이너를 실행할 수 있는 대부분의 운영 체제에서 실행되지만 모두가 동일하게 지원되지는 않습니다. 운영 체제는 사용자가 사용할 수 있는 지원 수준을 나타내는 계층으로 그룹화됩니다.

### <a name="tier-1"></a>계층 1
계층 1 시스템은 공식적으로 지원된다고 간주할 수 있습니다. 즉, Microsoft에서는
* 자동화된 테스트에서 이 운영 체제를 사용하고
* 해당 운영 체제에 대한 설치 패키지를 제공합니다.

일반 공급
| 운영 체제 | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Raspbian-stretch | 아니요 | 예|
| Ubuntu Server 16.04 | 예 | 아니요 |
| Ubuntu Server 18.04 | 예 | 아니요 |

공개 미리 보기
| 운영 체제 | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core 빌드 17763 | 예 | 아니요 |
| Windows 컨테이너용 Windows 10 빌드 17763<br><br>Linux 컨테이너용 Windows 10 빌드 14393 이상\* | 예 | 아니요 |
| Windows 컨테이너용 Windows Server 2019<br><br>Linux 컨테이너용 Windows Server 2016 이상\* | 예 | 아니요 |

\* Microsoft는 개발 및 테스트용으로만 Windows 디바이스에서 Linux 컨테이너용 설치 패키지를 제공합니다. 이는 프로덕션 용도로 지원되는 구성이 아닙니다. 

### <a name="tier-2"></a>계층 2
계층 2 시스템은 Azure IoT Edge와 호환 가능하다고 간주할 수 있고 상대적으로 쉽게 사용할 수 있습니다. 이는 다음을 의미합니다.
* Microsoft에서는 해당 플랫폼에 대한 임시 테스트를 완료하고 해당 플랫폼에서 성공적으로 Azure IoT Edge를 실행하는 파트너를 인식하고 있습니다.
* 다른 플랫폼에 대한 설치 패키지는 다음 플랫폼에서 작동할 수 있습니다.

| 운영 체제 | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | 예 | 예 |
| Debian 8 | 예 | 예 |
| Debian 9 | 예 | 예 |
| RHEL 7.5 | 예 | 예 |
| Ubuntu 18.04 | 예 | 예 |
| Ubuntu 16.04 | 예 | 예 |
| Wind River 8 | 예 | 아니요 |
| Yocto | 예 | 아니요 |

## <a name="container-engines"></a>컨테이너 엔진
Azure IoT Edge에는 실행되는 운영 체제에 관계 없이 모듈을 시작하기 위한 컨테이너 엔진이 필요합니다. Microsoft에서는 컨테이너 엔진인 Moby 엔진을 제공하여 이러한 요구 사항을 충족합니다. 해당 기능은 Moby 오픈 소스 프로젝트에 기반합니다. Docker CE 및 Docker EE도 널리 사용되는 다른 컨테이너 엔진이며, Moby 오픈 소스 프로젝트에 기반하고 Azure IoT Edge와 호환 가능합니다. Microsoft에서는 이러한 컨테이너 엔진을 사용하는 시스템에 대해 최고의 지원을 제공하지만 해당 시스템에서 발생한 문제를 수정하는 기능은 제공하지 않습니다. 이로 인해 Microsoft에서는 프로덕션 시스템에서 Moby 엔진을 사용하는 것이 좋습니다.

