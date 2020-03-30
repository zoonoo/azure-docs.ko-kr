---
title: Windows Azure VM 아키텍처 워크플로 | 마이크로 소프트 문서
description: 이 문서에서는 서비스를 배포할 때 워크플로 프로세스에 대한 개요를 제공합니다.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 5dd57a87658554bf59acf5cee1b6daf67b8692b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71162155"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Windows Azure 클래식 VM 아키텍처의 워크플로 
이 문서에서는 가상 컴퓨터와 같은 Azure 리소스를 배포하거나 업데이트할 때 발생하는 워크플로 프로세스에 대한 개요를 제공합니다. 

> [!NOTE]
>Azure는 리소스를 만들고 작업하기 위한 두 가지 배포 모델로 리소스 관리자와 클래식을 제공합니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다.

다음 다이어그램은 Azure 리소스의 아키텍처를 제공합니다.

![Azure 워크플로](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>워크플로우 기본 사항
   
**.** RDFE/FFE는 사용자로부터 패브릭으로의 통신 경로입니다. RDFE(RedDog 프런트 엔드)는 공개적으로 노출된 API로, 관리 포털및 Visual Studio, Azure MMC 등과 같은 서비스 관리 API의 프런트 엔드입니다.  사용자의 모든 요청은 RDFE를 통과합니다. FFE(패브릭 프런트 엔드)는 RDFE의 요청을 패브릭 명령으로 변환하는 레이어입니다. RDFE의 모든 요청은 FFE를 통과하여 패브릭 컨트롤러에 도달합니다.

**B**. 패브릭 컨트롤러는 데이터 센터의 모든 리소스를 유지 관리 및 모니터링합니다. 게스트 OS 버전, 서비스 패키지, 서비스 구성 및 서비스 상태와 같은 정보를 전송하는 패브릭 OS의 패브릭 호스트 에이전트와 통신합니다.

**C**. 호스트 에이전트는 호스트 OS에 거주하며 게스트 OS를 설정하고 게스트 에이전트(WindowsAzureGuestAgentAgent)와 통신하여 역할을 의도한 목표 상태로 업데이트하고 게스트 에이전트와 하트비트 검사를 수행합니다. 호스트 에이전트가 10분 동안 하트비트 응답을 받지 못하면 호스트 에이전트는 게스트 OS를 다시 시작합니다.

**C2**. WaAppAgent 설치, 구성 및 WindowsAzureGuestAgent.exe를 업데이트하는 것을 담당합니다.

**D**.  WindowsAzureGuestAgent는 다음에 대한 책임이 있습니다.

1. 방화벽, ACL, LocalStorage 리소스, 서비스 패키지 및 구성 및 인증서를 포함하여 게스트 OS를 구성합니다.
2. 역할이 실행될 사용자 계정에 대한 SID를 설정합니다.
3. 역할 상태를 패브릭에 전달합니다.
4. WaHostBootstrapper를 시작하고 모니터링하여 역할이 목표 상태에 있는지 확인합니다.

**E**. 와호스트부트래퍼는 다음을 담당합니다.

1. 역할 구성을 읽고 역할을 구성하고 실행하기 위해 모든 적절한 작업과 프로세스를 시작합니다.
2. 모든 자식 프로세스 모니터링.
3. 역할 호스트 프로세스에서 StatusCheck 이벤트를 발생시입니다.

**F**. 역할이 전체 IIS 웹 역할로 구성된 경우 IISConfigurator가 실행됩니다(SDK 1.2 HWC 역할에 대해 실행되지 않음). 다음을 담당합니다.

1. 표준 IIS 서비스 시작
2. 웹 구성에서 재작성 모듈 구성
3. 서비스 모델에서 구성된 역할에 대한 AppPool 설정
4. 진단 저장소 로컬 저장소 폴더를 가리키도록 IIS 로깅 설정
5. 사용 권한 및 ACL 구성
6. 웹 사이트는 %roleroot%:\사이트루트\0에 있으며 AppPool은 IIS를 실행하기 위해 이 위치를 가리킵니다. 

**G**. 시작 작업은 역할 모델에 의해 정의되고 WaHostBootstrapper에 의해 시작됩니다. 시작 작업은 백그라운드에서 비동기적으로 실행되도록 구성할 수 있으며 호스트 bootstrapper는 시작 작업을 시작한 다음 다른 시작 작업으로 계속 진행합니다. 시작 작업은 호스트 bootstrapper가 시작 작업이 실행을 완료하고 다음 시작 작업을 계속하기 전에 성공(0) 종료 코드를 반환할 때까지 기다리는 단순(기본) 모드에서 실행되도록 구성할 수도 있습니다.

**H**. 이러한 작업은 SDK의 일부이며 역할의 서비스 정의(.csdef)의 플러그인으로 정의됩니다. 시작 작업으로 확장 될 때 **DiagnosticsAgent** 및 **RemoteAccessAgent** 는 각각 두 개의 시작 작업(일반 작업 하나와 /blockStartup 매개 변수가 있는 작업)을 정의한다는 점에서 **고유합니다.** 일반 시작 작업은 역할 자체가 실행되는 동안 백그라운드에서 실행할 수 있도록 백그라운드 시작 작업으로 정의됩니다. **/blockStartup** 시작 작업은 간단한 시작 작업으로 정의되므로 WaHostBootstrapper가 계속하기 전에 종료될 때까지 기다립니다. **/blockStartup** 작업은 일반 작업이 초기화를 완료할 때까지 기다린 다음 종료하고 호스트 부트래퍼가 계속되도록 합니다. 이 작업은 역할 프로세스가 시작되기 전에 진단 및 RDP 액세스를 구성할 수 있도록 수행됩니다(/blockStartup 작업을 통해 수행). 또한 호스트 bootstrapper가 시작 작업을 완료한 후에도 진단 및 RDP 액세스가 계속 실행될 수 있습니다(일반 작업을 통해 수행).

**나는**. WaWorkerHost는 일반 작업자 역할에 대한 표준 호스트 프로세스입니다. 이 호스트 프로세스는 OnStart 및 Run과 같은 모든 역할의 DDL 및 진입점 코드를 호스트합니다.

**J**. WaWebHost는 SDK 1.2 호환 호스팅 웹 코어(HWC)를 사용하도록 구성된 웹 역할의 표준 호스트 프로세스입니다. 역할은 서비스 정의(.csdef)에서 요소를 제거하여 HWC 모드를 활성화할 수 있습니다. 이 모드에서는 모든 서비스의 코드와 DLL이 WaWebHost 프로세스에서 실행됩니다. IIS(w3wp)는 사용되지 않으며 IIS가 WaWebHost.exe 내에서 호스팅되기 때문에 IIS 관리자에 구성된 앱풀이 없습니다.

**K**. WaIISHost는 전체 IIS를 사용하는 웹 역할에 대한 역할 진입점 코드의 호스트 프로세스입니다. 이 프로세스는 **RoleEntryPoint** 클래스를 사용하고 이 클래스(OnStart, Run, OnStop)에서 코드를 실행하는 첫 번째 DLL을 로드합니다. RoleEntryPoint 클래스에서 생성된 모든 **역할환경** 이벤트(예: StatusCheck 및 변경됨)는 이 프로세스에서 발생합니다.

**L**. W3WP는 역할이 전체 IIS를 사용하도록 구성된 경우 사용되는 표준 IIS 작업자 프로세스입니다. 이렇게 하면 IISConfigurationator에서 구성된 앱풀이 실행됩니다. 여기에 작성된 모든 역할 환경 이벤트(예: StatusCheck 및 변경됨)는 이 프로세스에서 발생합니다. 두 프로세스에서 이벤트를 구독하면 역할 환경 이벤트가 두 위치(WaIISHost 및 w3wp.exe)에서 모두 발생합니다.

## <a name="workflow-processes"></a>워크플로우 프로세스

1. 사용자는 ".cspkg" 및 ".cscfg" 파일 업로드, 중지 또는 구성 변경 등을 중지하도록 리소스에 알리는 등의 요청을 합니다. 이 작업은 Azure 포털 또는 Visual Studio 게시 기능과 같은 서비스 관리 API를 사용하는 도구를 통해 수행할 수 있습니다. 이 요청은 RDFE로 이동하여 모든 구독 관련 작업을 수행한 다음 요청을 FFE에 전달합니다. 이러한 워크플로 단계의 나머지 단계는 새 패키지를 배포하고 시작하는 것입니다.
2. FFE는 올바른 컴퓨터 풀(선호도 그룹 또는 지리적 위치 및 장비 가용성과 같은 패브릭의 입력과 같은 고객 입력에 따라)을 찾아 해당 컴퓨터 풀의 마스터 패브릭 컨트롤러와 통신합니다.
3. 패브릭 컨트롤러는 사용 가능한 CPU 코어가 있는 호스트를 찾거나 새 호스트를 회전시면 됩니다. 서비스 패키지 및 구성이 호스트에 복사되고 패브릭 컨트롤러가 호스트 OS의 호스트 에이전트와 통신하여 패키지를 배포합니다(DIP, 포트, 게스트 OS 구성 등).
4. 호스트 에이전트는 게스트 OS를 시작하고 게스트 에이전트(WindowsAzureGuestAgentAgent)와 통신합니다. 호스트는 게스트에게 하트비트를 전송하여 역할이 목표 상태를 향해 작동하는지 확인합니다.
5. WindowsAzureGuestAgent 게스트 OS(방화벽, ACL, 로컬 스토리지 등)를 설정하고 새 XML 구성 파일을 c:\Config에 복사한 다음 WaHostBootstrapper 프로세스를 시작합니다.
6. 전체 IIS 웹 역할의 경우 WaHostBootstrapper는 IIS구성을 시작하고 IIS에서 웹 역할에 대한 기존 앱풀을 삭제하라고 지시합니다.
7. WaHostBootstrapper는 E:\RoleModel.xml에서 **시작** 작업을 읽고 시작 작업 실행을 시작합니다. WaHostBootstrapper는 모든 간단한 시작 작업이 완료되고 "성공" 메시지를 반환할 때까지 기다립니다.
8. 전체 IIS 웹 역할의 경우 WaHostBootstrapper는 IISConfigurationator에게 IIS AppPool을 구성하도록 지시하고 사이트를 `E:\Sitesroot\<index>`가리키며 0 기반 `<index>` 인덱스는 서비스에 대해 정의된 요소 수로 `<Sites>` 가리킵니다.
9. WaHostBootstrapper는 역할 유형에 따라 호스트 프로세스를 시작합니다.
    1. **작업자 역할**: WaWorkerHost.exe가 시작됩니다. WaHostBootstrapper는 OnStart() 메서드를 실행합니다. 반환 후 WaHostBootstrapper Run() 메서드를 실행 하기 시작 하 고 동시에 준비로 역할을 표시 하 고 로드 밸런서 회전에 넣습니다 (InputEndpoints가 정의 된 경우). 그런 다음 WaHostBootsrapper는 역할 상태를 확인하는 루프로 이동합니다.
    1. **SDK 1.2 HWC 웹 역할**: WaWebHost가 시작됩니다. WaHostBootstrapper는 OnStart() 메서드를 실행합니다. 반환 후 WaHostBootstrapper Run() 메서드를 실행 하기 시작 하 고 동시에 준비로 역할을 표시 하 고 로드 밸런서 회전에 넣습니다. WaWebHost는 워밍업 요청(GET /do.rd_runtime_init)을 발행합니다. 모든 웹 요청은 WaWebHost.exe로 전송됩니다. 그런 다음 WaHostBootsrapper는 역할 상태를 확인하는 루프로 이동합니다.
    1. **전체 IIS 웹 역할**: aIISHost가 시작됩니다. WaHostBootstrapper는 OnStart() 메서드를 실행합니다. 반환 후 Run() 메서드를 실행 하기 시작 하 고 동시에 준비로 역할을 표시 하 고 로드 밸런서 회전에 넣습니다. 그런 다음 WaHostBootsrapper는 역할 상태를 확인하는 루프로 이동합니다.
10. 전체 IIS 웹 역할에 대한 웹 요청이 들어오면 IIS가 W3WP 프로세스를 시작하고 온-프레미스 IIS 환경에서와 동일하게 요청을 처리하도록 트리거됩니다.

## <a name="log-file-locations"></a>로그 파일 위치

**WindowsAzureGuestAgent**

- C:\로그\AppAgentRuntime.Log.  
이 로그에는 시작, 중지 및 새 구성을 포함한 서비스에 대한 변경 내용이 포함되어 있습니다. 서비스가 변경되지 않으면 이 로그 파일에 큰 시간 차이가 있을 것으로 예상할 수 있습니다.
- C:\로그\WaAppAgent.Log.  
이 로그는 상태 업데이트 및 하트비트 알림을 포함하며 2~3초마다 업데이트됩니다.  이 로그에는 인스턴스 상태에 대한 기록 보기가 포함되어 있으며 인스턴스가 Ready 상태에 있지 않은 시기를 알려줍니다.
 
**와호스트부츠래퍼**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**와웹호스트**

`C:\Resources\Directory\<guid>.<role>\WaWebHost.log`
 
**와이스호스트**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IIS구성기**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**IIS 로그**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**윈도우 이벤트 로그**

`D:\Windows\System32\Winevt\Logs`
 



