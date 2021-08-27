---
title: Windows Azure VM 아키텍처 워크플로 | Microsoft Docs
description: 본 문서에서는 서비스 배포 시의 워크플로 프로세스 개요를 제공합니다.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: aeaf371782f725265433f587809d363d932fc189
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821764"
---
# <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Windows Azure 클래식 VM 아키텍처 워크플로 

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

본 문서에서는 가상 머신 등의 Azure 리소스를 배포하거나 업데이트할 때 발생하는 워크플로 프로세스에 대한 개요를 제공합니다. 

> [!NOTE]
>Azure는 리소스를 만들고 작업하기 위한 두 가지 배포 모델로 리소스 관리자와 클래식을 제공합니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다.

다음 다이어그램은 Azure 리소스 아키텍처를 제공합니다.

:::image type="content" source="./media/cloud-services-workflow-process/workflow.jpg" alt-text="<alt Azure 워크플로 이미지>":::

## <a name="workflow-basics"></a>워크플로 기본 사항
   
**A**. RDFE / FFE는 사용자와 패브릭 간의 통신 경로입니다. RDFE(RedDog 프런트 엔드)는 Visual Studio, Azure MMC 등과 같은 Service Management API 및 관리 포털의 프런트 엔드인 퍼블릭 API입니다.  사용자의 요청은 모두 RDFE를 거쳐 전달됩니다. FFE(패브릭 프런트 엔드)는 RDFE에서 오는 요청을 패브릭 명령으로 변환하는 계층입니다. RDFE의 요청은 모두 FFE를 거쳐 패브릭 컨트롤러에 도달합니다.

**B**. 패브릭 컨트롤러는 데이터 센터 내의 모든 리소스를 유지하고 모니터링하는 일을 담당합니다. 패브릭 컨트롤러는 게스트 OS 버전, 서비스 패키지, 서비스 구성, 서비스 상태 등의 정보를 전송하는 패브릭 OS 상의 패브릭 호스트 에이전트와 통신합니다.

**C**. 호스트 에이전트는 호스트 OS 내에 있으면서 의도한 목표 상태에 대한 역할을 업데이트하고 게스트 에이전트로 하트비트 확인을 하기 위한 게스트 OS 설정과 게스트 에이전트(WindowsAzureGuestAgent)와의 통신을 담당합니다. 호스트 에이전트는 10분 동안 하트비트 응답을 받지 못하면 게스트 OS를 재시작합니다.

**C2**. WaAppAgent는 WindowsAzureGuestAgent.exe의 설치, 구성, 업데이트를 담당합니다.

**D**.  WindowsAzureGuestAgent는 다음을 담당합니다.

1. 방화벽, ACLs, LocalStorage 리소스, 서비스 패키지 및 구성, 인증서를 포함한 게스트 OS의 구성
2. 해당 역할을 실행할 사용자 계정에 대한 SID 설정
3. 패브릭에 해당 역할 상태 전달
4. 해당 역할이 목표한 상태에 도달하였는지 확인하기 위하여 WaHostBootstrapper 시작 및 모니터링

**E**. WaHostBootstrapper는 다음을 담당합니다.

1. 역할 구성 읽기 및 해당 역할의 구성과 실행을 위하여 적절한 작업과 프로세스를 모두 시작
2. 자식 프로세스를 모두 모니터링
3. 해당 역할 호스트 프로세스에 StatusCheck 이벤트 발생

**F**. 역할이 전체 IIS 웹 역할로 구성된 경우, IISConfigurator가 실행됩니다. IISConfigurator는 다음을 담당합니다.

1. 표준 IIS 서비스 시작
2. 웹 구성의 다시 쓰기 모듈 구성
3. 서비스 모델에 구성된 역할에 대한 AppPool 설정
4. IIS 로깅이 DiagnosticStore LocalStorage 폴더를 가리키도록 설정
5. 권한 및 ACLs 구성
6. 웹사이트의 위치는 %roleroot%:\sitesroot\0이며 AppPool은 이 위치를 가리켜 IIS를 실행합니다. 

**G**. 시작 작업은 역할 모델이 정의하며 WaHostBootstrapper가 시작합니다. 백그라운드 비동기로 시작 작업을 실행하도록 구성할 수 있으며 호스트 부트스트래퍼는 시작 작업을 시작한 다음 다른 시작 작업으로 이어갑니다. 또한, 시작 작업은 다음 시작 작업으로 넘어가기 전에 실행을 완료하고 성공(0) 종료 코드를 반환할 때까지 호스트 부트스트래퍼가 기다리는 간단(기본) 모드에서 실행하도록 구성할 수도 있습니다.

**H**. 이러한 작업은 SDK의 일부이며 역할 서비스 정의(.csdef)에서는 플러그인으로 정의됩니다. 시작 작업으로 확장할 때, **DiagnosticsAgent** 와 **RemoteAccessAgent** 는 각자 일반적인 시작 작업 하나와 **/blockStartup** 매개 변수가 있는 시작 작업 하나로 이루어진 시작 작업 두 개를 정의한다는 점에서 고유합니다. 정상적인 시작 작업은 역할 자체가 실행되는 동안 백그라운드에서 실행될 수 있도록 백그라운드 시작 작업으로 정의됩니다. **/blockStartup** 시작 작업은 단순 시작 작업으로 정의되어, 계속하기 전에 종료될 때까지 WaHostBootstrapper가 기다리도록 합니다. **/blockStartup** 작업은 일반 작업이 초기화 될 때까지 기다렸다가 종료되어 호스트 부트스트래퍼가 계속할 수 있게 합니다. 이를 통하여 진단 및 RDP 액세스를 역할 프로세스 시작 전에 구성할 수 있습니다(/blockStartup 작업을 통하여 실행됩니다). 또한 이를 통하여 호스트 부트스트래퍼가 시작 작업을 마친 뒤에도 진단 및 RDP 액세스가 계속 실행되도록 합니다(일반 작업을 통하여 실행됩니다).

**I**. WaWorkerHost는 일반 작업자 역할에 대한 표준 호스트 프로세스입니다. 이 호스트 프로세스는 역할의 DLL과 진입점 코드(예: OnStart 및 Run)를 모두 호스트합니다.

**J**. WaIISHost는 전체 IIS를 사용하는 웹 역할의 역할 진입점 코드에 대한 호스트 프로세스입니다. 해당 프로세스는 **RoleEntryPoint** 클래스를 사용하고 이 클래스에서 코드(OnStart, Run, OnStop)를 실행하는 것으로 확인된 첫 번째 DLL을 로드합니다. RoleEntryPoint 클래스에서 만들어지는 **RoleEnvironment** 이벤트(StatusCheck나 Changed 등)는 모두 해당 프로세스에서 발생합니다.

**K**. W3WP는 역할이 전체 IIS를 사용하도록 구성된 경우 사용되는 표준 IIS 작업자 프로세스입니다. 이를 통하여 IISConfigurator에서 구성한 AppPool이 실행됩니다. 여기에서 만들어지는 RoleEnvironment 이벤트(StatusCheck나 Changed 등)는 모두 해당 프로세스에서 발생합니다. 양쪽 프로세스에서 동시에 이벤트를 구독하는 경우 RoleEnvironment 이벤트는 WaIISHost 및 w3wp.exe에서 동시에 발생합니다.

## <a name="workflow-processes"></a>워크플로 프로세스

1. 사용자는 리소스로 하여금 구성 변경 중단이나 진행 등을 전달하면서 ".cspkg" 및 ".cscfg" 파일 업로드 등의 요청을 합니다. 이러한 작업은 Azure Portal을 통하여 또는 Visual Studio Publish 기능과 같은 Service Management API를 사용하는 도구를 거쳐 실행합니다. 이러한 요청은 RDFE에 전달되어 구독 관련 작업을 모두 수행한 뒤 해당 요청을 FFE에 전달합니다. 워크플로의 나머지 단계는 새로운 패키지를 배포하여 시작하는 것입니다.
2. FFE는 선호도 그룹이나 지리적 위치 및 컴퓨터 가용성 등과 같이 패브릭에서 전달되는 입력 등의 사용자 지정 입력에 기초하여 올바른 컴퓨터 풀을 찾고 해당 컴퓨터 풀의 마스터 패브릭 컨트롤러와 통신합니다.
3. 패브릭 컨트롤러는 CPU 코어를 사용할 수 있거나 새로운 호스트를 스핀업하는 호스트를 찾습니다. 서비스 패키지 및 구성은 해당 호스트로 복사되며 패브릭 컨트롤러는 호스트 OS 상에서 해당 호스트 에이전트와 통신하여 해당 패키지(구성 DIP, 포트, 게스트 OS 등)를 배포합니다.
4. 호스트 에이전트는 게스트 OS를 시작하고 게스트 에이전트(WindowsAzureGuestAgent)와 통신합니다. 호스트는 역할이 목표 상태를 향하여 작동하고 있는지를 확인하기 위하여 게스트에 하트비트를 보냅니다.
5. WindowsAzureGuestAgent는 게스트 OS(방화벽, ACL, LocalStorage 등)를 설정하고 새로운 XML 구성을 c:\Config에 복사한 다음, WaHostBootstrapper 프로세스를 시작합니다.
6. 전체 IIS 웹 역할의 경우, WaHostBootstrapper는 IISConfigurator를 시작하여 IIS에서 시작된 웹 역할에 대한 기존 AppPools를 삭제하도록 지시합니다.
7. WaHostBootstrapper는 E:\RoleModel.xml에서 **시작** 작업을 읽고 시작 작업을 실행 시작합니다. WaHostBootstrapper는 단순 시작 작업이 모두 완료되어 “완료” 메시지를 반환할 때까지 대기합니다.
8. 전체 IIS 웹 역할의 경우, WaHostBootstrapper는 IISConfigurator가 IIS AppPool을 구성하도록 지시하고, `<index>`가 0부터 시작하여 서비스가 거부된 `<Sites>` 요소의 수에 도달하는 인덱스인 `E:\Sitesroot\<index>`로 사이트를 가리키도록 합니다.
9. WaHostBootstrapper는 다음 역할 유형에 따라 호스트 프로세스를 시작합니다.
    1. **작업자 역할**: WaWorkerHost.exe가 시작합니다. WaHostBootstrapper는 OnStart() 메서드를 실행합니다. 반환 후, WaHostBootstrapper는 Run() 메서드 실행을 시작한 다음, 역할을 준비로 표시하면서 부하 분산 장치 순환에 배치합니다(InputEndpoints가 정의된 경우). 이후, WaHostBootsrapper는 해당 역할 상태를 확인하는 루프로 이동합니다.
    2. **전체 IIS 웹 역할**: aIISHost가 시작합니다. WaHostBootstrapper는 OnStart() 메서드를 실행합니다. 반환 후에는 Run() 메서드 실행을 시작한 다음, 역할을 준비로 표시하면서 부하 분산 장치 순환에 배치합니다. 이후, WaHostBootsrapper는 해당 역할 상태를 확인하는 루프로 이동합니다.
10. 전체 IIS 웹 역할에 들어오는 웹 요청은 온-프레미스 IIS 환경에서와 마찬가지로 IIS가 W3WP 프로세스를 시작하고 해당 요청을 처리하도록 유도합니다.

## <a name="log-file-locations"></a>로그 파일 위치

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
시작, 중지, 새로운 설정 등을 포함하는 서비스 관련 변경 사항이 들어 있는 로그입니다. 서비스가 변경되지 않으면 해당 로그 파일에 상당한 시간 간격이 생길 것으로 예상할 수 있습니다.
- C:\Logs\WaAppAgent.Log.  
상태 업데이트와 하트비트 알림이 들어 있는 로그이며, 2~3초마다 업데이트됩니다.  인스턴스 상태에 대한 기록 보기가 들어 있는 로그이며, 인스턴스가 준비 상태가 아닌 경우에 이를 알려 줍니다.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**IIS 로그**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Windows 이벤트 로그**

`D:\Windows\System32\Winevt\Logs`
