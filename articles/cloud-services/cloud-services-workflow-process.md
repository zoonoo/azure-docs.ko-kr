---
title: Windows Azure VM 아키텍처의 워크플로 | Microsoft Docs
description: 이 문서는 서비스를 배포할 때 워크플로 프로세스의 개요를 제공 합니다.
services: cloud-services
documentationcenter: ''
author: genlin
manager: Willchen
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 7c8459a6694663a49203b6ec21a760d3e6bd60c3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60480763"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Windows Azure 클래식 VM 아키텍처의 워크플로 
이 문서를 배포 하거나 가상 컴퓨터와 같은 Azure 리소스를 업데이트할 때 발생 하는 워크플로 프로세스의 개요를 제공 합니다. 

> [!NOTE]
>Azure에는 리소스를 만들고 사용하기 위한 Resource Manager 및 클래식 합니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다.

다음 다이어그램은 Azure 리소스의 아키텍처를 제공합니다.

![Azure 워크플로](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>워크플로 기본 사항
   
**A**. RDFE FFE 패브릭에 사용자의 통신 경로 /입니다. RDFE (RedDog 프런트 엔드)에 관리 포털에 프런트 엔드는 공개적으로 노출 된 API 및 Visual Studio, Azure MMC 등과 같은 서비스 관리 API가 합니다.  사용자의 모든 요청 RDFE를 통해 이동합니다. FFE (패브릭 프런트 엔드) 계층에 fabric 명령은 RDFE에서 요청을 변환 하는 경우 RDFE에서 모든 요청을 통해 패브릭 컨트롤러에 연결할 FFE 이동 합니다.

**B**. 패브릭 컨트롤러는 유지 관리 하 고 데이터 센터에 있는 모든 리소스를 모니터링 하는 일을 담당 합니다. 게스트 OS 버전, 서비스 패키지, 서비스 구성 및 서비스 상태와 같은 정보를 보내는 OS 패브릭에서 패브릭 호스트 에이전트를 사용 하 여 통신합니다.

**C**. 호스트 에이전트 호스트 OSsystem에 및 게스트 OS의 설정에 대 한 일을 담당 하며 게스트 에이전트를 사용 하 여 확인 상태를 돕기 위한 목적으로 역할을 업데이트 하 고 하트 비트를 수행 하기 위해 게스트 에이전트 (WindowsAzureGuestAgent)와 통신 합니다. 호스트 에이전트는 10 분 동안 하트 비트 응답을 수신 하지 않습니다, 게스트 OS 호스트 에이전트 다시 시작 합니다.

**C2**. WaAppAgent는 설치, 구성 및 WindowsAzureGuestAgent.exe를 업데이트 하는 일을 담당 합니다.

**D**.  WindowsAzureGuestAgent는 다음 담당 합니다.

1. 방화벽, Acl, LocalStorage 리소스, 서비스 패키지 및 구성 및 인증서를 포함 하 여 게스트 OS를 구성 합니다. 역할이 실행 하는 사용자 계정에 대 한 SID를 설정 합니다.
2. 패브릭 역할 상태를 통신 합니다.
3. WaHostBootstrapper를 시작 하 고 있는지 모니터링 목표 상태에서 역할이입니다.

**E**. WaHostBootstrapper는 담당 합니다.

1. 역할 구성을 읽고 모든 적절 한 작업 및 구성 하 고 역할을 실행 하는 프로세스를 시작 합니다.
2. 모든 하위 프로세스를 모니터링 합니다.
3. 역할 호스트 프로세스에 StatusCheck 이벤트를 발생 시키는 합니다.

**F**. IISConfigurator는 역할이 전체 IIS 웹 역할 (실행 되지 것입니다 SDK 1.2 HWC 역할)로 구성 된 경우 실행 됩니다. 이 담당 합니다.

1. 표준 IIS 서비스를 시작합니다.
2. 웹 구성에서 재작성 모듈 구성
3. 서비스 모델에 구성 된 역할에 대 한 AppPool 설정
4. IIS 로깅 DiagnosticStore LocalStorage 폴더를 가리키도록 설정
5. 사용 권한 및 Acl 구성
6. 웹 사이트는 IIS를 실행 하기 위해이 위치로 apppool 지점과 %roleroot%:\sitesroot\0 상주 합니다. 

**G**. 시작 작업 역할 모델에 의해 정의 되며 WaHostBootstrapper에 의해 시작 됩니다. 백그라운드에서 비동기적으로 실행 하도록 시작 작업을 구성할 수 있습니다 및 호스트 부트스트래퍼 시작 작업이 시작 되며 그런 다음 다른 시작 작업을 진행 합니다. 시작 작업 실행이 완료 되 고 다음 시작 작업을 계속 하기 전에 (0) 성공 종료 코드를 반환 하는 시작 작업에 대 한 호스트 부트스트래퍼 대기할 단순 (기본값) 모드에서 실행 되도록 구성할 수도 있습니다.

**H**. 이러한 SDK의 일부인 작업과 역할의 서비스 정의 (.csdef)에 대 한 플러그 인으로 정의 됩니다. 시작 작업으로 확장 하는 경우는 **DiagnosticsAgent** 하 고 **RemoteAccessAgent** 은 두 가지 시작 작업, 하나의 일반 및가 정의 하는 각각에 고유를 **/blockStartup** 매개 변수입니다. 일반적인 시작 작업은 역할 자체가 실행 되는 동안 백그라운드에서 실행할 수 있도록 백그라운드 시작 작업으로 정의 됩니다. 합니다 **/blockStartup** 간단한 시작 작업으로 시작 작업은 정의 WaHostBootstrapper 계속 하기 전에 종료 되도록 기다립니다. 합니다 **/blockStartup** 태스크가 초기화를 완료 하려면 일반적인 작업을 대기 하 고 종료 하 고 계속 하려면 호스트 부트스트래퍼를 허용 합니다. 이 수행 되 면 (이렇게 /blockStartup 작업) 역할 프로세스가 시작 되기 전에 진단 및 RDP 액세스를 구성할 수 있습니다. 또한 진단 및 계속 호스트 부트스트래퍼 (일반 작업을 통해 수행 됩니다) 시작 작업이 완료 된 후 실행에 대 한 RDP 액세스 수 있습니다.

**I**. WaWorkerHost은 일반 작업자 역할에 대 한 표준 호스트 프로세스입니다. 이 호스트 프로세스는 모든 역할의 Dll 및 진입점 코드를 OnStart 실행 등을 호스팅합니다.

**J**. WaWebHost는 웹 역할에 대 한 표준 호스트 프로세스는 SDK 1.2 호환 호스팅 가능한 웹 코어 HWC ()를 사용 하도록 구성 된 경우. 역할은 서비스 정의 (.csdef)에서 요소를 제거 하 여 HWC 모드를 설정할 수 있습니다. 이 모드에서는 모든 서비스의 코드 및 Dll WaWebHost 프로세스에서 실행 합니다. IIS (w3wp)를 사용 하지 않으면 되며 없습니다 AppPools WaWebHost.exe 내 IIS에서 호스팅되는 IIS 관리자에서 구성 합니다.

**K**. WaIISHost는 역할이 전체 IIS를 사용 하는 웹 역할에 대 한 진입점 코드에 대 한 호스트 프로세스입니다. 이 프로세스는 첫 번째 DLL 로드 있는 사용 하는 합니다 **RoleEntryPoint** 클래스 및 (OnStart, 실행, OnStop)이이 클래스에서 코드를 실행 합니다. 모든 **RoleEnvironment** RoleEntryPoint 클래스에서 만든 이벤트 (예: StatusCheck 및 변경 됨)이이 프로세스에서 발생 합니다.

**L**. W3WP는 역할이 전체 IIS를 사용 하도록 구성 된 경우 사용 되는 표준 IIS 작업자 프로세스입니다. 이 IISConfigurator에서 구성 된 AppPool을 실행 합니다. 여기에서 만들어진 모든 RoleEnvironment 이벤트 (예: StatusCheck 및 변경 됨)는이 프로세스에서 발생 합니다. 두 프로세스 모두에서 이벤트를 구독할 경우 두 위치 (WaIISHost 및 w3wp.exe) RoleEnvironment 이벤트가 발생 하는 참고 합니다.

## <a name="workflow-processes"></a>워크플로 프로세스

1. 사용자가.cspkg 및.cscfg 파일을 업로드, 등을 중지 하려면 리소스 라는 구성 변경 요청, 등입니다. Azure portal 또는 Visual Studio 게시 기능 등 서비스 관리 API를 사용 하는 도구를 통해이 수행할 수 있습니다. 이 요청의 모든는 구독 및 관련 된 작동 FFE 요청을 통신 하 할 RDFE로 이동 합니다. 워크플로 단계 나머지 하는 새 패키지를 배포 하 고 시작 됩니다.
2. FFE (패브릭 컴퓨터 가용성 등의 입력 또는 선호도 그룹 및 지리적 위치와 같은 고객 의견에 따라) 올바른 컴퓨터 풀을 찾아서 해당 컴퓨터 풀에서 마스터 패브릭 컨트롤러와 통신 합니다.
3. 패브릭 컨트롤러는 호스트에 사용 가능한 CPU 코어를 찾습니다 (또는 사용자가 작동 한 새 호스트). 서비스 패키지 및 구성을 호스트에 복사 되 고 패브릭 컨트롤러는 호스트 패키지를 배포 하는 OS에서 호스트 에이전트와 통신 (Dip, 포트, 게스트 OS 및 등 구성).
4. 게스트 OS를 시작 하 고 게스트 에이전트 (WindowsAzureGuestAgent)와 통신 하는 호스트 에이전트. 호스트 역할 목표 상태로 방향으로 작동 하는지 확인 하려면 게스트에 게 하트 비트를 보냅니다.
5. WindowsAzureGuestAgent은 게스트 OS (방화벽, Acl, LocalStorage, 및 등)을 설정 하 고, c:\Config, 새 XML 구성 파일을 복사, 다음 WaHostBootstrapper 프로세스를 시작 합니다.
6. 전체 IIS 웹 역할에 대 한 WaHostBootstrapper IISConfigurator를 시작 하 고 IIS에서 웹 역할에 대 한 모든 기존 AppPools를 삭제 하도록 알려 줍니다.
7. WaHostBootstrapper 읽습니다 합니다 **시작** E:\RoleModel.xml에서 작업 하 고 시작 작업 실행을 시작 합니다. WaHostBootstrapper 모든 간단한 시작 작업 완료 있고 "성공" 메시지가 반환 될 때까지 대기 합니다.
8. 전체 IIS 웹 역할에 대 한 WaHostBootstrapper 지시 IISConfigurator IIS AppPool을 구성 하 고 사이트를 가리키는 `E:\Sitesroot\<index>`, 여기서 `<index>` 개수는 0 기반된 인덱스는 <Sites> 서비스에 대해 정의 된 요소입니다.
9. WaHostBootstrapper 역할 유형에 따라 호스트 프로세스가 시작 됩니다.
    1. **작업자 역할**: WaWorkerHost.exe 시작 됩니다. WaHostBootstrapper는 onstart () 메서드를 실행 합니다. 반환 된 후 WaHostBootstrapper run () 메서드를 실행 하 고 동시에 준비 역할로 표시 시작한 (InputEndpoints 정의 됨) 하는 경우 부하 분산 장치 순환에 배치 합니다. 그런 다음 WaHostBootsrapper 역할 상태를 확인 하는 루프를 돕니다.
    1. **SDK 1.2 HWC 웹 역할**: WaWebHost 시작 됩니다. WaHostBootstrapper는 onstart () 메서드를 실행 합니다. 반환 된 후 WaHostBootstrapper 시작 run () 메서드를 실행 하 고 준비 된 상태로 역할을 동시에 표시 하 고 부하 분산 장치 순환에 넣습니다. WaWebHost 준비 요청 (GET /do.rd_runtime_init)을 발급 합니다. 모든 웹 요청 WaWebHost.exe에 전송 됩니다. 그런 다음 WaHostBootsrapper 역할 상태를 확인 하는 루프를 돕니다.
    1. **전체 IIS 웹 역할**: aIISHost 시작 됩니다. WaHostBootstrapper는 onstart () 메서드를 실행 합니다. 반환 된 후 run () 메서드를 실행 하기 시작 하 고 준비 된 상태로 역할을 동시에 표시 하 고 부하 분산 장치 순환에 배치 합니다. 그런 다음 WaHostBootsrapper 역할 상태를 확인 하는 루프를 돕니다.
10. 전체 IIS로 들어오는 웹 요청에는 역할 트리거 W3WP 프로세스를 시작 하 고 온-프레미스 IIS 환경에서와 마찬가지로 동일한 요청을 처리 하는 IIS 웹입니다.

## <a name="log-file-locations"></a>로그 파일 위치

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
이 로그를 시작, 중지 되 고 새 구성을 비롯 한 서비스 변경 내용을 포함 합니다. 서비스 변경 되지 않는 경우에이 로그 파일에 큰 간격이 시간 표시를 예상할 수 있습니다.
- C:\Logs\WaAppAgent.Log.  
이 로그는 상태 업데이트 및 하트 비트 알림을 포함 하 고 2 ~ 3 초 마다 업데이트 됩니다.  이 로그는 인스턴스 상태를 기록 보기를 포함 및 알려 인스턴스에서 준비 상태가 없습니다.
 
**WaHostBootstrapper**

C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log
 
**WaWebHost**

C:\Resources\Directory\<guid>.<role>\WaWebHost.log
 
**WaIISHost**

C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log
 
**IISConfigurator**

C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log
 
**IIS 로그**

C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1
 
**Windows 이벤트 로그**

D:\Windows\System32\Winevt\Logs
 



