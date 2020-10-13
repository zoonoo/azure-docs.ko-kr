---
title: 지원되는 운영 체제, 컨테이너 엔진 - Azure IoT Edge
description: Azure IoT Edge 디먼 및 런타임을 실행할 수 있는 운영 체제와 프로덕션 디바이스의 지원되는 컨테이너 엔진에 대해 알아봅니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 27d078bc1dc61079d44110999b70c5195c9c8a2a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874223"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge 지원 시스템

이 문서에서는 공식적으로 또는 미리 보기에서 IoT Edge가 지원하는 시스템 및 구성 요소에 대한 세부 정보를 제공합니다.

Azure IoT Edge 서비스를 사용하는 동안 문제가 발생하는 경우 지원을 검색하는 몇 가지 방법이 있습니다. 지원을 받으려면 다음 채널 중 하나를 시도합니다.

**버그 보고** – Azure IoT Edge 제품에 대한 대다수의 개발은 IoT Edge 오픈 소스 제품에서 이루어집니다. 버그는 프로젝트의 [문제 페이지](https://github.com/azure/iotedge/issues)에서 보고됩니다. 프로젝트에서 제품 업데이트로 신속하게 문제를 해결합니다.

**Microsoft 고객 지원팀** – [지원 계획](https://azure.microsoft.com/support/plans/)이 있는 사용자는 [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)에서 직접 지원 티켓을 만들어서 Microsoft 고객 지원팀과 협력할 수 있습니다.

**기능 요청** – Azure IoT Edge 제품은 제품의 [사용자 의견 페이지](https://feedback.azure.com/forums/907045-azure-iot-edge)를 통해 기능 요청을 추적합니다.

## <a name="container-engines"></a>컨테이너 엔진

Azure IoT Edge 모듈은 컨테이너로 구현되므로 모듈을 시작하려면 컨테이너 엔진이 필요합니다. Microsoft에서는 컨테이너 엔진인 Moby 엔진을 제공하여 이러한 요구 사항을 충족합니다. 이 컨테이너 엔진은 Moby 오픈 소스 프로젝트를 기준으로 합니다. Docker CE 및 Docker EE도 널리 사용되는 다른 컨테이너 엔진이며, Moby 오픈 소스 프로젝트를 기반으로 하고 Azure IoT Edge와 호환됩니다. Microsoft에서는 이러한 컨테이너 엔진을 사용하는 시스템에 대해 최고의 지원을 제공하지만 해당 시스템에서 발생한 문제를 수정할 수 없습니다. 이로 인해 Microsoft에서는 프로덕션 시스템에서 Moby 엔진을 사용하는 것이 좋습니다.

<br>
<center>

![컨테이너 런타임으로서의 Moby 엔진](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>운영 체제

Azure IoT Edge는 컨테이너를 실행할 수 있는 대부분의 운영 체제에서 실행되지만 이러한 모든 시스템이 동일하게 지원되지는 않습니다. 운영 체제는 사용자가 사용할 수 있는 지원 수준을 나타내는 계층으로 그룹화됩니다.

* 계층 1 시스템이 지원됩니다. 계층 1 시스템의 경우 Microsoft는 다음을 수행합니다.
  * 자동화된 테스트에서 이 운영 체제를 사용하고
  * 해당 운영 체제에 대한 설치 패키지를 제공합니다.
* 계층 2 시스템은 Azure IoT Edge와 호환되며 상대적으로 쉽게 사용할 수 있습니다. 계층 2 시스템:
  * Microsoft에서는 해당 플랫폼에 대한 비공식적 테스트를 완료하고 해당 플랫폼에서 성공적으로 Azure IoT Edge를 실행하는 파트너를 인식하고 있습니다.
  * 다른 플랫폼에 대한 설치 패키지는 다음 플랫폼에서 작동할 수 있습니다.

호스트 OS 제품군은 항상 모듈의 컨테이너 내부에서 사용되는 게스트 OS 제품군과 일치해야 합니다. 즉, Linux에서는 Linux 컨테이너만 사용하고 Windows에서는 Windows 컨테이너만 사용할 수 있습니다. Windows를 사용하는 경우 프로세스 격리 컨테이너만 지원되고 Hyper-V 격리 컨테이너는 지원되지 않습니다.  

<br>
<center>

![호스트 OS가 게스트 OS와 일치함](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>계층 1

다음 표에 나열된 시스템은 일반 공급되거나 퍼블릭 미리 보기 방식으로 Microsoft에서 지원되며 각 새 릴리스로 테스트되었습니다. 

| 운영 체제 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | 퍼블릭 미리 보기  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | 퍼블릭 미리 보기 |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), 빌드 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), 빌드 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), 빌드 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), 빌드 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

위에 나열된 Windows 운영 체제는 Windows에서 Windows 컨테이너를 실행하는 디바이스에 대한 요구 사항으로, 프로덕션 환경에서 유일하게 지원되는 구성입니다. Windows용 Azure IoT Edge 설치 패키지를 통해 Windows에서 Linux 컨테이너를 사용할 수 있지만 이 구성은 개발 및 테스트 전용입니다. 자세한 내용은 [Windows에서 IoT Edge를 사용하여 Linux 컨테이너 실행](how-to-install-iot-edge-windows-with-linux.md)을 참조하세요.

### <a name="tier-2"></a>계층 2

다음 표에 나열된 시스템은 Azure IoT Edge 호환 는 것으로 간주되지만 Microsoft에서 적극적으로 테스트하거나 유지 관리하지 않습니다.

| 운영 체제 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20.04 <sup>2</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Raspian Buster를 포함하는 Debian 10 시스템은 IoT Edge에서 지원하지 않는 OpenSSL 버전을 사용합니다. IoT Edge를 설치하기 전에 다음 명령을 사용하여 이전 버전을 설치합니다.

```bash
sudo apt-get install libssl1.0.2
```

<sup>2</sup> [Azure IoT Edge 릴리스 리포지토리의](https://github.com/Azure/azure-iotedge/releases) Debian 9 패키지는 Ubuntu 20.04를 사용 하 여 즉시 작동 해야 합니다.

## <a name="releases"></a>릴리스

IoT Edge 릴리스 자산과 릴리스 정보는 [azure-iotedge 릴리스](https://github.com/Azure/azure-iotedge/releases) 페이지에서 사용할 수 있습니다. 이 섹션에서는 각 버전의 구성 요소를 보다 쉽게 시각화하는 데 유용한 릴리스 정보에 대한 정보를 반영합니다.

IoT Edge 구성 요소는 개별적으로 설치 또는 업데이트할 수 있으며 이전 버전의 구성 요소와 호환됩니다. 다음 표에서는 각 릴리스에 포함된 구성 요소를 보여 줍니다.

| 해제 | 보안 디먼 | 에지 허브<br>에지 에이전트 | Libiothsm | Moby |
|--|--|--|--|--|
| **1.0.9** | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 |  |
| **1.0.8** | 1.0.8 | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4(ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

IoT Edge는 Microsoft.Azure.Device.Client SDK를 사용합니다. 자세한 내용은 [Azure IoT C# SDK GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-csharp) 또는 [.NET용 Azure SDK 참조 콘텐츠](/dotnet/api/overview/azure/iot/client)를 참조하세요. 다음 목록에서는 각 릴리스가 테스트되는 클라이언트 SDK 버전을 보여 줍니다.

* **IoT Edge 1.0.9**: Client SDK 1.21.1
* **IoT Edge 1.0.8**: Client SDK 1.20.3
* **IoT Edge 1.0.7**: Client SDK 1.20.1
* **IoT Edge 1.0.6**: Client SDK 1.17.1
* **IoT Edge 1.0.5**: Client SDK 1.17.1

## <a name="virtual-machines"></a>Virtual Machines

가상 머신에서 Azure IoT Edge를 실행할 수 있습니다. 일반적으로 고객이 에지 인텔리전스를 사용하여 기존 인프라를 보강하려는 경우 가상 머신을 IoT Edge 디바이스로 사용하는 것이 일반적입니다. 호스트 VM OS 제품군은 모듈의 컨테이너 내부에서 사용되는 게스트 OS 제품군과 일치해야 합니다. 이 요구 사항은 Azure IoT Edge가 디바이스에서 직접 실행될 때와 동일합니다. Azure IoT Edge는 기본 가상화 기술에 중립적이며, Hyper-V 및 vSphere와 같은 플랫폼을 통해 구동되는 VM에서 작동합니다.

<br>
<center>

![VM의 Azure IoT Edge](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>최소 시스템 요구 사항

Azure IoT Edge는 Raspberry Pi3만큼 작은 디바이스부터 서버 등급 하드웨어까지 잘 실행됩니다. 시나리오에 적합한 하드웨어 선택은 실행하려는 워크로드에 따라 다릅니다. 최종 디바이스 결정은 복잡할 수 있습니다. 그러나 기존 노트북이나 데스크톱에서 쉽게 솔루션 프로토타입 생성을 시작할 수 있습니다.

프로토타입 생성 경험은 최종 디바이스 선택에 도움이 됩니다. 고려해야 할 질문은 다음과 같습니다.

* 워크로드에 있는 모듈은 몇 개나 되나요?
* 모듈의 컨테이너는 몇 개의 계층을 공유하나요?
* 모듈을 작성할 때 사용한 언어는 무엇인가요?
* 모듈에서 처리할 데이터의 양은 얼마나 되나요?
* 워크로드를 가속화하기 위해 모듈에 전문화된 하드웨어가 필요한가요?
* 솔루션의 원하는 성능 특성은 무엇인가요?
* 하드웨어 예산은 얼마나 되나요?
