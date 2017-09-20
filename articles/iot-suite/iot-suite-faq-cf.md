---
title: "Azure IoT Suite 연결된 팩터리 FAQ | Microsoft Docs"
description: "IoT Suite 연결된 팩터리에 대한 질문과 대답"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: corywink
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 35cf824210a14410d7ea2aedddde0040309901f9
ms.contentlocale: ko-kr
ms.lasthandoff: 08/25/2017

---
# <a name="frequently-asked-questions-for-iot-suite-connected-factory-preconfigured-solution"></a>IoT Suite 연결된 팩터리 미리 구성된 솔루션에 대한 질문과 대답

IoT Suite에 대한 일반 [FAQ](iot-suite-faq.md)도 참조하세요.

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solution"></a>어디에서 미리 구성된 솔루션의 소스 코드를 찾을 수 있습니까?

소스 코드는 다음 GitHub 리포지토리에 저장됩니다.

* [연결된 팩터리 미리 구성된 솔루션](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>OPC UA란?

2008년에 발표된 OPC UA(통합 아키텍처)는 플랫폼 독립적이며 서비스 지향 상호 운용성 표준입니다. OPC UA는 산업 PC, PLC 및 센서와 같은 다양한 산업 시스템 및 장치에서 사용됩니다. OPC UA는 OPC 클래식 사양의 기능을 보안이 기본적으로 제공되는 확장 가능한 단일 프레임워크에 통합합니다. 이 표준은 OPC Foundation에서 운영합니다. [OPC Foundation](http://opcfoundation.org/)은 440명 이상의 구성원이 있는 비영리 조직입니다. 조직의 목표는 OPC 사양을 사용하여 다음을 통해 다중 공급업체, 다중 플랫폼, 안전하고 신뢰할 수 있는 상호 운용성을 지원하는 것입니다.

* 인프라
* 사양
* 기술
* 프로세스

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-preconfigured-solution"></a>Microsoft가 연결된 팩터리 미리 구성된 솔루션으로 OPC UA를 선택한 이유

Microsoft는 플랫폼 독립적이고 업계에서 통용되며 입증된 오픈 비독점 표준인 OPC UA를 선택했습니다. 광범위한 제조 프로세스 및 장비 간의 상호 운용성을 보장하는 Industrie 4.0(RAMI4.0) 참조 아키텍처 솔루션을 위해 이 제품이 필요합니다. Microsoft는 Industrie 4.0 솔루션을 빌드하려는 고객의 요청을 확인했습니다. OPC UA에 대한 지원은 고객의 목표 달성을 가로막는 장애물을 해소하는 데 도움을 주고 즉각적인 비즈니스 가치를 제공합니다.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>공용 IP 주소를 시뮬레이션 VM에 어떻게 추가하나요?

IP 주소를 추가하는 두 가지 옵션이 있습니다.

* [리포지토리](https://github.com/Azure/azure-iot-connected-factory)에서 PowerShell 스크립트 `Simulation/Factory/Add-SimulationPublicIp.ps1`을 사용합니다. 배포 이름을 매개 변수로 전달합니다. 로컬 배포의 경우 `<your username>ConnFactoryLocal`을 사용합니다. 스크립트는 VM의 IP 주소를 출력합니다.

* Azure Portal에서 배포의 리소스 그룹을 찾습니다. 로컬 배포의 경우를 제외하고 리소스 그룹에는 솔루션 또는 배포 이름으로 지정한 이름이 있습니다. 빌드 스크립트를 사용하는 로컬 배포의 경우 리소스 그룹의 이름은 `<your username>ConnFactoryLocal`입니다. 이제 리소스 그룹에 새 **공용 IP 주소** 리소스를 추가합니다.

> [!NOTE]
> 두 경우에서 [Ubuntu 웹 사이트](https://wiki.ubuntu.com/Security/Upgrades)의 지침을 따라 최신 패치를 설치했는지 확인합니다. 공용 IP 주소를 통해 VM에 액세스할 수 있는 한 설치를 최신 상태로 유지합니다.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>시뮬레이션 VM에 대한 공용 IP 주소를 어떻게 제거하나요?

IP 주소를 제거하는 두 가지 옵션이 있습니다.

* [리포지토리](https://github.com/Azure/azure-iot-connected-factory)의 PowerShell 스크립트 Simulation/Factory/Remove-SimulationPublicIp.ps1을 사용합니다. 배포 이름을 매개 변수로 전달합니다. 로컬 배포의 경우 `<your username>ConnFactoryLocal`을 사용합니다. 스크립트는 VM의 IP 주소를 출력합니다.

* Azure Portal에서 배포의 리소스 그룹을 찾습니다. 로컬 배포의 경우를 제외하고 리소스 그룹에는 솔루션 또는 배포 이름으로 지정한 이름이 있습니다. 빌드 스크립트를 사용하는 로컬 배포의 경우 리소스 그룹의 이름은 `<your username>ConnFactoryLocal`입니다. 이제 리소스 그룹에서 **공용 IP 주소** 리소스를 제거합니다.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>시뮬레이션 VM에 어떻게 로그인하나요?

[리포지토리](https://github.com/Azure/azure-iot-connected-factory)에서 PowerShell 스크립트 `build.ps1`을 사용하여 솔루션을 배포한 경우에만 시뮬레이션 VM에 대한 로그인이 지원됩니다.

www.azureiotsuite.com에서 솔루션을 배포한 경우 VM에 로그인할 수 없습니다. 암호는 임의로 생성되고 다시 설정할 수 없으므로 로그인할 수 없습니다.

1. VM에 공용 IP 주소를 추가합니다. [공용 IP 주소를 시뮬레이션 VM에 어떻게 추가하나요?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)를 참조하세요.
1. VM의 IP 주소를 사용하여 VM에 대한 SSH 세션을 만듭니다.
1. 사용할 사용자 이름은 `docker`입니다.
1. 사용할 암호는 배포하는 데 사용한 버전에 따라 다릅니다.
    * 2017년 6월 1일 이전에 build.ps1 스크립트를 사용하여 배포된 솔루션의 경우 암호는 `Passw0rd`입니다.
    * 2017년 6월 1일 이후에 build.ps1 스크립트를 사용하여 배포된 솔루션의 경우 `<name of your deployment>.config.user` 파일에서 암호를 찾을 수 있습니다. 암호는 **VmAdminPassword** 설정에 저장됩니다. `build.ps1` 스크립트 매개 변수 `-VmAdminPassword`를 사용하여 암호를 지정하지 않은 한 암호는 배포 시 임의로 생성됩니다.

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>시뮬레이션 VM에서 모든 docker 프로세스를 어떻게 중지하고 시작하나요?

1. 시뮬레이션 VM에 로그인합니다. [시뮬레이션 VM에 어떻게 로그인하나요?](#how-do-i-sign-in-to-the-simulation-vm)를 참조하세요.
1. 활성화되어 있는 컨테이너를 확인하려면 `docker ps`를 실행합니다.
1. 모든 시뮬레이션 컨테이너를 중지하려면 `./stopsimulation`을 실행합니다.
1. 모든 시뮬레이션 컨테이너를 시작하려면:
    * **IOTHUB_CONNECTIONSTRING** 이름으로 셸 변수를 내보냅니다. `<name of your deployment>.config.user` 파일에서 **IotHubOwnerConnectionString** 설정의 값을 사용합니다. 예:

        ```
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * `./startsimulation`을 실행합니다.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>VM에서 시뮬레이션을 어떻게 업데이트하나요?

시뮬레이션에 변경 사항을 만든 경우 `updatedimulation` 명령을 사용하여 [리포지토리](https://github.com/Azure/azure-iot-connected-factory)에서 PowerShell 스크립트 `build.ps1`을 사용할 수 있습니다. 이 스크립트는 모든 시뮬레이션 구성 요소를 빌드하고, VM에서 시뮬레이션을 중지하고, 모든 시뮬레이션 구성 요소를 업로드, 설치 및 시작합니다.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>내 솔루션에서 사용하는 IoT 허브의 연결 문자열을 어떻게 찾나요?

[리포지토리](https://github.com/Azure/azure-iot-connected-factory)에서 `build.ps1` 스크립트를 사용하여 솔루션을 배포한 경우 연결 문자열은 `<name of your deployment>.config.user` 파일에서 **IotHubOwnerConnectionString**의 값입니다.

Azure Portal을 사용하여 연결 문자열을 찾을 수도 있습니다. 배포의 리소스 그룹의 IoT Hub 리소스에서 연결 문자열 설정을 찾습니다.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>연결된 팩터리 시뮬레이션에서 어떤 IoT Hub 장치를 사용하나요?

시뮬레이션은 다음 장치를 자체 등록합니다.

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

[DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) 또는 [iothub-explorer](https://github.com/azure/iothub-explorer) 도구를 사용하여 솔루션이 사용 중인 IoT 허브로 등록된 장치를 확인할 수 있습니다. 이러한 도구를 사용하려면 배포에서 IoT 허브에 대한 연결 문자열이 필요합니다.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>시뮬레이션 구성 요소에서 로그 데이터는 어떻게 얻을 수 있나요?

시뮬레이션에서 모든 구성 요소는 로그 파일에 정보를 기록합니다. 이러한 파일은 `home/docker/Logs` 폴더의 VM에서 찾을 수 있습니다. 로그를 검색하기 위해 [리포지토리](https://github.com/Azure/azure-iot-connected-factory)에서 PowerShell 스크립트 `Simulation/Factory/Get-SimulationLogs.ps1`을 사용할 수 있습니다.

이 스크립트는 VM에 로그인해야 합니다. 로그인에 대한 자격 증명을 제공해야 합니다. 자격 증명을 찾으려면 [시뮬레이션 VM에 어떻게 로그인하나요?](#how-do-i-sign-in-to-the-simulation-vm)를 참조하세요.

공용 IP 주소가 아직 없거나 제거하는 경우 스크립트는 VM에 대한 공용 IP 주소를 추가/제거합니다. 스크립트는 보관 파일에 모든 로그 파일을 저장하고 개발 워크스테이션으로 보관 파일을 다운로드합니다.

또는 SSH를 통해 VM에 로그인하고 런타임 시 로그 파일을 검사합니다.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>시뮬레이션이 데이터를 클라우드로 보내는지 어떻게 확인할 수 있나요?

[DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) 또는 [iothub-explorer](https://github.com/azure/iothub-explorer) 도구를 사용하여 특정 장치에서 IoT Hub로 전송된 데이터를 검사할 수 있습니다. 이러한 도구를 사용하려면 배포에서 IoT 허브에 대한 연결 문자열을 알아야 합니다. [내 솔루션에서 사용하는 IoT 허브의 연결 문자열을 어떻게 찾나요?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)를 참조하세요.

게시자 장치 중 하나를 통해 보낸 데이터를 검사합니다.

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

IoT Hub로 전송된 데이터를 볼 수 없는 경우 시뮬레이션에 문제가 있습니다. 첫 번째 분석 단계로 시뮬레이션 구성 요소의 로그 파일을 분석해야 합니다. [시뮬레이션 구성 요소에서 로그 데이터는 어떻게 얻을 수 있나요?](#how-can-i-get-log-data-from-the-simulation-components)를 참조하세요. 다음으로 시뮬레이션 중지 및 시작을 시도하고, 전송된 데이터가 여전히 없는 경우 시뮬레이션을 완전히 업데이트합니다. [VM에서 시뮬레이션을 어떻게 업데이트하나요?](#how-do-i-update-the-simulation-in-the-vm)를 참조하세요.

### <a name="next-steps"></a>다음 단계

미리 구성된 IoT Suite 솔루션의 몇 가지 다른 기능 및 기능을 탐색할 수 있습니다.

* [예측 유지 관리 사전 구성 솔루션 개요](iot-suite-predictive-overview.md)
* [연결된 팩터리 미리 구성된 솔루션 개요](iot-suite-connected-factory-overview.md)
* [처음부터 IoT 보안을 고려](securing-iot-ground-up.md)
