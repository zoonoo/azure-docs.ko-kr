---
title: Azure Stack을 사용 하 여 외부 모니터링 솔루션 통합 | Microsoft Docs
description: 데이터 센터의 외부 모니터링 솔루션을 사용 하 여 Azure Stack을 통합 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 02/06/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: ff28fbb52b33308967051a37fdaa3c6c273fc282
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816100"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Azure Stack을 사용 하 여 외부 모니터링 솔루션 통합

Azure Stack 인프라의 외부 모니터링에 대 한 Azure Stack 소프트웨어, 물리적 컴퓨터 및 실제 네트워크 스위치를 모니터링 해야 합니다. 이러한 각 분야는 상태 및 경고 정보를 검색 하는 메서드를 제공 합니다.

- Azure Stack 소프트웨어 상태 및 경고를 검색 하는 REST 기반 API를 제공 합니다. 저장소 공간 다이렉트, 저장소 상태 및 경고와 같은 소프트웨어 정의 기술의 사용을 사용 하면 소프트웨어 모니터링에 속합니다.
- 물리적 컴퓨터 가능 상태 및 경고 정보 베이스 보드 관리 컨트롤러 (Bmc)를 통해 사용할 수 있습니다.
- 실제 네트워크 장치 가능 상태 및 경고 정보 SNMP 프로토콜을 통해 사용할 수 있습니다.

하드웨어 수명 주기 호스트와 각 Azure Stack 솔루션 제공 됩니다. 이 호스트는 물리적 서버 및 네트워크 장치에 대 한 원래 장비 제조업체 (OEM) 하드웨어 공급 업체의 모니터링 소프트웨어를 실행합니다. 원한다 면 무시 하는 솔루션을 모니터링 하 고 직접 데이터 센터에서 기존 모니터링 솔루션을 사용 하 여을 통합할 수 있습니다.

> [!IMPORTANT]
> 사용할 외부 모니터링 솔루션은 에이전트 없는 이어야 합니다. Azure Stack 구성 요소 내에서 타사 에이전트를 설치할 수 없습니다.

다음 다이어그램에서는 Azure Stack 통합 시스템, 하드웨어 수명 주기 호스트, 외부 모니터링 솔루션 및 외부 티켓팅/데이터 컬렉션 시스템을 간의 트래픽 흐름을 보여 줍니다.

![Azure Stack, 모니터링 및 티켓팅 솔루션 간의 트래픽을 보여 주는 다이어그램입니다.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> 네트워크 장치 및 물리적 서버와 직접 외부 Monitoring Integration 허용 및 액세스 제어 목록 (Acl)에 의해 적극적으로 차단 합니다. 

이 문서에서는 System Center Operations Manager 및 Nagios와 같은 외부 모니터링 솔루션과 Azure Stack을 통합 하는 방법에 설명 합니다. 또한 REST API 호출을 통해 또는 PowerShell을 사용 하 여 경고를 사용 하 여 프로그래밍 방식으로 작업 하는 방법을 포함 합니다.

## <a name="integrate-with-operations-manager"></a>Operations Manager와 통합

Azure Stack의 외부 모니터링에 대 한 Operations Manager를 사용할 수 있습니다. Microsoft Azure Stack 용 System Center 관리 팩을 사용 하면 단일 Operations Manager 인스턴스를 사용 하 여 여러 Azure Stack 배포를 모니터링할 수 있습니다. 관리 팩 상태 리소스 공급자 및 업데이트 리소스 공급자 REST Api 사용 하 여 Azure Stack을 사용 하 여 통신 합니다. 하드웨어 수명 주기 호스트에서 실행 되는 소프트웨어를 모니터링 하는 OEM 무시 하려는 경우에 물리적 서버를 모니터링 하는 공급 업체 관리 팩을 설치할 수 있습니다. 또한 네트워크 스위치를 모니터링 하려면 Operations Manager 네트워크 장치 검색을 사용할 수 있습니다.

Azure Stack 용 관리 팩은 다음 기능을 제공합니다.

- 여러 Azure Stack 배포를 관리할 수 있습니다.
- Azure Active Directory (Azure AD) 및 Active Directory Federation Services (AD FS)에 대 한 지원
- 검색 하 고 경고를 종결
- 상태 및 용량 대시보드
- 패치 및 업데이트 (& U) 인 경우 진행률에 대 한 자동 유지 관리 모드 검색 포함
- 배포 및 지역에 대 한 작업을 강제로 업데이트를 포함합니다.
- 지역에 사용자 지정 정보를 추가할 수 있습니다.
- 지원 알림 및 보고

Microsoft Azure Stack 및 연결 된 System Center 관리 팩을 다운로드할 수 있습니다 [사용자 가이드](https://www.microsoft.com/en-us/download/details.aspx?id=55184), 또는 Operations Manager에서 직접.

티켓팅 솔루션의 경우 Operations Manager와 System Center Service Manager를 통합할 수 있습니다. 통합된 제품 커넥터는 Service Manager에서 서비스 요청을 해결 한 후 Azure Stack 및 Operations Manager에서 경고를 종결 하려면 있는 양방향 통신을 가능 합니다.

다음 다이어그램은 기존 System Center 배포를 사용 하 여 Azure Stack의 통합을 보여줍니다. Azure Stack에서 작업을 실행 하는 System Center Orchestrator 또는 SMA Service Management Automation ()를 사용 하 여 Service Manager를 더욱 자동화할 수 있습니다.

![SMA OM, Service Manager와의 통합을 보여 주는 다이어그램입니다.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Nagios와 통합

플러그 인을 모니터링 하는 Nagios 파트너 Cloudbase 솔루션에서 함께 개발 되었습니다 관대 한 무료 소프트웨어 라이선스 – MIT (매사추세츠 Institute of Technology)에서 사용할 수 있습니다.

플러그 인 Python에서 기록 되 고 상태 리소스 공급자 REST API를 활용 합니다. 검색 및 Azure Stack에서 경고를 종결 하기 위한 기본 기능을 제공 합니다. System Center 관리 팩과 같은 수 있도록 여러 Azure Stack 배포를 추가 하 고 알림을 보낼 수 있습니다.

Nagios 엔터프라이즈 및 Nagios Core를 사용 하 여 플러그 인이 작동 합니다. [여기](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)에서 다운로드할 수 있습니다. 또한 다운로드 사이트 설치 및 구성 세부 정보를 포함합니다.

### <a name="plugin-parameters"></a>플러그 인 매개 변수

다음 매개 변수를 사용 하 여 플러그 인 파일 "Azurestack_plugin.py"를 구성 합니다.

| 매개 변수 | 설명 | 예 |
|---------|---------|---------|
| *arm_endpoint* | Azure 리소스 관리자 (관리자) 끝점 |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Azure 리소스 관리자 (관리자) 끝점  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | 관리자 구독 ID | 관리자 포털 또는 PowerShell을 통해 검색 |
| *User_name* | 연산자 구독 사용자 이름 | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | 연산자 구독 암호 | mypassword |
| *Client_id* | 클라이언트 | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Azure Stack 지역 이름 | local |
|  |  |

* PowerShell GUID 제공 되는 유니버설입니다. 각 배포에 대해 사용할 수 있습니다.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>PowerShell을 사용 하 여 상태 모니터링 및 경고

Operations Manager, Nagios, 또는 Nagios 기반 솔루션을 사용 하지 않는 경우에 모니터링 Azure Stack과 함께 통합 하는 솔루션의 광범위 한 범위를 사용 하도록 설정 하려면 PowerShell을 사용할 수 있습니다.

1. PowerShell을 사용 하려면 했는지 [PowerShell 설치 및 구성](azure-stack-powershell-configure-quickstart.md) Azure Stack 연산자 environment에 대 한 합니다. 리소스 관리자 (관리자) 끝점에 연결할 수 있는 로컬 컴퓨터에 PowerShell을 설치 (https://adminmanagement. [ 지역]입니다. [External_FQDN])입니다.

2. Azure Stack 운영자로 서 Azure Stack 환경에 연결 하려면 다음 명령을 실행 합니다.

   ```PowerShell  
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. 다음 예제와 같은 명령을 사용 하 여 경고를 사용 하려면:
   ```PowerShell
    #Retrieve all alerts
    Get-AzsAlert

    #Filter for active alerts
    $Active=Get-AzsAlert | Where {$_.State -eq "active"}
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    Get-AzsRPHealth

    #Retrieve infrastructure role instance health
    $FRPID=Get-AzsRPHealth|Where-Object {$_.DisplayName -eq "Capacity"}
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId

    ```

## <a name="learn-more"></a>자세한 정보

기본 제공 상태 모니터링에 대 한 자세한 내용은 [Azure Stack의 상태 및 경고 모니터링](azure-stack-monitor-health.md)합니다.

## <a name="next-steps"></a>다음 단계

[보안 통합](azure-stack-integrate-security.md)
