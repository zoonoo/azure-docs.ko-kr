---
title: 지원되는 운영 체제, 컨테이너 엔진 - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge 디먼 및 런타임을 실행할 수 있는 운영 체제와 프로덕션 디바이스의 지원되는 컨테이너 엔진에 대해 알아봅니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 781884b0973ca42db542f53d452dbaaf7a7f1fa7
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973289"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge 지원 시스템

이 문서에서는 공식적으로 또는 미리 보기에서 IoT Edge에서 지 원하는 시스템 및 구성 요소에 대 한 세부 정보를 제공 합니다. 

Azure IoT Edge 서비스를 사용 하는 동안 문제가 발생 하는 경우 지원을 검색 하는 몇 가지 방법이 있습니다. 지원에 대 한 다음 채널 중 하나를 시도 합니다.

**버그 보고** – Azure IoT Edge 제품에 대한 대다수의 개발은 IoT Edge 오픈 소스 제품에서 이루어집니다. 버그는 프로젝트의 [문제 페이지](https://github.com/azure/iotedge/issues)에서 보고됩니다. 프로젝트에서 제품 업데이트로 신속하게 문제를 해결합니다.

**Microsoft 고객 지원팀** – [지원 계획](https://azure.microsoft.com/support/plans/)이 있는 사용자는 [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)에서 직접 지원 티켓을 만들어서 Microsoft 고객 지원팀과 협력할 수 있습니다.

**기능 요청** – Azure IoT Edge 제품은 제품의 [사용자 의견 페이지](https://feedback.azure.com/forums/907045-azure-iot-edge)를 통해 기능 요청을 추적합니다.

## <a name="container-engines"></a>컨테이너 엔진

Azure IoT Edge 모듈은 컨테이너로 구현 되므로 IoT Edge 컨테이너 엔진이이를 시작 해야 합니다. Microsoft에서는 컨테이너 엔진인 Moby 엔진을 제공하여 이러한 요구 사항을 충족합니다. 이 컨테이너 엔진은 Moby 오픈 소스 프로젝트를 기반으로 합니다. Docker CE 및 Docker EE도 널리 사용되는 다른 컨테이너 엔진이며, 또한 Moby 오픈 소스 프로젝트를 기반으로 하며 Azure IoT Edge와 호환 됩니다. Microsoft는 이러한 컨테이너 엔진을 사용 하 여 시스템에 대 한 최상의 지원을 제공 합니다. 그러나 Microsoft는 문제에 대 한 픽스를 제공할 수 없습니다. 이로 인해 Microsoft에서는 프로덕션 시스템에서 Moby 엔진을 사용하는 것이 좋습니다.

<br>
<center>

![Moby를 컨테이너 런타임](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>운영 체제
Azure IoT Edge는 컨테이너를 실행할 수 있는 대부분의 운영 체제에서 실행 됩니다. 그러나 이러한 모든 시스템은 동일 하 게 지원 되지 않습니다. 운영 체제는 사용자가 사용할 수 있는 지원 수준을 나타내는 계층으로 그룹화됩니다.
* 계층 1 시스템이 지원 됩니다. 계층 1 시스템의 경우 Microsoft:
    * 자동화된 테스트에서 이 운영 체제를 사용하고
    * 해당 운영 체제에 대한 설치 패키지를 제공합니다.
* 계층 2 시스템은 Azure IoT Edge와 호환 되며 비교적 쉽게 사용할 수 있습니다. 계층 2 시스템:
    * Microsoft는 플랫폼에 대 한 임시 테스트를 완료 했거나 플랫폼에서 Azure IoT Edge 성공적으로 실행 되는 파트너를 알고 있습니다.
    * 다른 플랫폼에 대한 설치 패키지는 다음 플랫폼에서 작동할 수 있습니다.
    
호스트 OS 제품군은 항상 모듈의 컨테이너 내부에서 사용되는 게스트 OS 제품군과 일치해야 합니다. 즉, Linux에서는 Linux 컨테이너만 사용하고 Windows에서는 Windows 컨테이너만 사용할 수 있습니다. Windows를 사용 하는 경우 Hyper-v 격리 컨테이너가 아닌 프로세스 격리 컨테이너만 지원 됩니다.  

<br>
<center>

![호스트 OS는 게스트 OS](./media/support/edge-on-device.png)
와 일치 </center>

### <a name="tier-1"></a>계층 1

다음 표에 나열 된 시스템은 일반적으로 사용 가능 하거나 공개 미리 보기로 제공 되는 Microsoft에서 지원 되며 각 새 릴리스로 테스트 됩니다. 

| 운영 체제 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | 공개 미리 보기  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | 공개 미리 보기 |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), 빌드 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), 빌드 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), 빌드 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), 빌드 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |


위에 나열 된 Windows 운영 체제는 Windows에서 Windows 컨테이너를 실행 하는 장치에 대 한 요구 사항입니다. 프로덕션에 대해 유일 하 게 지원 되는 구성입니다. Windows 용 Azure IoT Edge 설치 패키지를 사용 하면 Windows에서 Linux 컨테이너를 사용할 수 있습니다. 그러나이 구성은 개발 및 테스트용 으로만 사용할 수 있습니다. 자세한 내용은 [Windows에서 IoT Edge를 사용 하 여 Linux 컨테이너 실행](how-to-install-iot-edge-windows-with-linux.md)을 참조 하세요.

### <a name="tier-2"></a>계층 2

다음 표에 나열 된 시스템은 Azure IoT Edge 호환 되는 것으로 간주 되지만 Microsoft에서 적극적으로 테스트 하거나 유지 관리 하지는 않습니다.

| 운영 체제 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [전문가 Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![전문가 Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![전문가 Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![전문가 Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [전문가 Embedded Linux 전체 운영 체제](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![전문가 Embedded Linux 전체 OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![전문가 Embedded Linux 전체 OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [바람 강 8](https://docs.windriver.com/category/os-wind_river_linux) | ![바람 강 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian Raspian Buster를 포함 한 시스템은 지원 하지 않는 IoT Edge OpenSSL 버전을 사용 합니다. IoT Edge를 설치 하기 전에 다음 명령을 사용 하 여 이전 버전을 설치 합니다. 

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>Virtual Machines
가상 머신에서 Azure IoT Edge를 실행할 수 있습니다. 고객이 Edge intelligence를 사용 하 여 기존 인프라를 보강 하려는 경우에는 가상 머신을 IoT Edge 장치로 사용 하는 것이 일반적입니다. 호스트 VM OS 제품군은 모듈의 컨테이너 내부에서 사용되는 게스트 OS 제품군과 일치해야 합니다. 이 요구 사항은 Azure IoT Edge 장치에서 직접 실행 되는 경우와 동일 합니다. Azure IoT Edge는 기본 가상화 기술에 중립적이며, Hyper-V 및 vSphere와 같은 플랫폼을 통해 구동되는 VM에서 작동합니다.

<br>
<center>

VM](./media/support/edge-on-vm.png)
에서 Azure IoT Edge를 ![</center>

## <a name="minimum-system-requirements"></a>최소 시스템 요구 사항
Azure IoT Edge는 Raspberry Pi3만큼 작은 디바이스부터 서버 등급 하드웨어까지 잘 실행됩니다. 시나리오에 적합 한 하드웨어를 선택 하는 것은 실행 하려는 워크 로드에 따라 달라 집니다. 최종 디바이스 결정은 복잡할 수 있습니다. 그러나 기존 노트북이나 데스크톱에서 쉽게 솔루션 프로토타입 생성을 시작할 수 있습니다.

프로토타입 생성 경험은 최종 디바이스 선택에 도움이 됩니다. 고려해 야 할 사항은 다음과 같습니다. 

* 워크 로드에 있는 모듈은 몇 개입니까?
* 모듈의 컨테이너에서 얼마나 많은 계층을 공유 하나요?
* 모듈에서 작성 한 언어는 무엇 인가요? 
* 모듈에서 처리할 데이터의 양은 얼마 인가요?
* 작업 부하를 가속화 하기 위해 모듈에 전문화 된 하드웨어가 필요 한가요?
* 솔루션의 원하는 성능 특성은 무엇 인가요?
* 하드웨어 예산은 무엇 인가요?
