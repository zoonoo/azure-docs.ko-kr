---
title: "Azure Government 모니터링 및 관리 | Microsoft 문서"
description: "이 설명서에서는 Azure Government의 응용 프로그램 개발에 대한 기능 및 지침을 비교합니다."
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 1/13/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: ec62cc79aeffa21e5d9d772dcd2da6f973c18d81
ms.openlocfilehash: 3d9f2308d20e723da324be7e3aec3106ff5ff846


---
# <a name="azure-government-monitoring--management"></a>Azure Government 모니터링 및 관리
이 문서에서는 Azure Government 환경의 모니터링 및 관리 서비스 변형과 고려 사항을 개략적으로 설명합니다.

## <a name="automation"></a>자동화
Automation은 일반적으로 Azure Government에서 사용할 수 있습니다.

### <a name="variations"></a>변형
다음 Automation 기능은 현재 Azure Government에서 사용할 수 없습니다.

* 인증에 사용할 서비스 주체 자격 증명 만들기

자세한 내용은 [Automation 공개 문서](../automation/automation-intro.md)를 참조하세요.

## <a name="backup"></a>Backup
Backup은 일반적으로 Azure Government에서 사용할 수 있습니다.

자세한 내용은 [Azure Government Backup](documentation-government-services-backup.md)을 참조하세요.

## <a name="site-recovery"></a>사이트 복구
Site Recovery(ASR)는 일반적으로 Azure Government에서 사용할 수 있습니다.

자세한 내용은 [Site Recovery 공개 문서](../site-recovery/site-recovery-overview.md)를 참조하세요.

### <a name="variations"></a>변형
현재 Azure Government에서 사용할 수 없는 Site Recovery 기능은 다음과 같습니다.

* Azure Resource Manager 사이트 복구 자격 증명 모음
* 메일 알림

| 사이트 복구 | 클래식 | 리소스 관리자 |
| --- | --- | --- |
| VMware/물리적  | GA | GA |
| Hyper-V | GA | GA |
| 사이트-사이트 | GA | GA |

>[!NOTE]
>표는 미국 버지니아 주 정부 및 미국 아이오와 주 정부에 적용됩니다.

Azure Government에서 ASR에 대한 다음 URL은 서로 다릅니다.

| Azure 공용 | Azure Government | 참고 사항 |
| --- | --- | --- |
| *.hypervrecoverymanager.windowsazure.com | *. hypervrecoverymanager.windowsazure.us | Site Recovery 서비스 액세스 |
| *. *.backup.windowsazure.com  | *.backup.windowsazure.us | 보호 서비스 액세스 |
| *.blob.core.windows.net | *.blob.core.usgovcloudapi.net | VM 스냅숏 저장 |
| http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | MySQL 다운로드 |

## <a name="log-analytics"></a>Log Analytics
Log Analytics는 일반적으로 Azure Government에서 사용할 수 있습니다.

### <a name="variations"></a>변형
다음 Log Analytics 기능과 솔루션은 현재 Azure Government에서 사용할 수 없습니다.

* 다음은 Mcrosoft Azure에서 미리 보기로 제공되는 솔루션입니다.
  * 네트워크 모니터링 솔루션
  * 서비스 맵
  * Office 365 솔루션
  * Windows 10 Analytics 업그레이드 솔루션
  * Application Insights 솔루션
  * Azure 네트워킹 분석 솔루션
  * Azure Automation 분석 솔루션
  * Key Vault 분석 솔루션
* 다음은 온-프레미스 소프트웨어 업데이트가 필요한 솔루션 및 기능입니다.
  * Integration with System Center Operations Manager 2016(이전 버전의 Operations Manager는 지원됨)
  * System Center Configuration Manager의 컴퓨터 그룹
  * Surface Hub 솔루션
* 다음은 공용 Azure에서 미리 보기로 제공되는 기능입니다.
  * Power BI로 데이터 내보내기
* Azure 메트릭 및 Azure 진단
* Operations Management Suite 모바일 응용 프로그램

Azure Government에서 Log Analytics의 URL은 서로 다릅니다.

| Azure 공용 | Azure Government | 참고 사항 |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Log Analytics 포털 |
| *workspaceId*.ods.opinsights.azure.com |*workspaceId*.ods.opinsights.azure.us |[데이터 수집기 API](../log-analytics/log-analytics-data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*.ods.opinsights.azure.us |에이전트 통신 - [방화벽 설정 구성](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*.oms.opinsights.azure.us |에이전트 통신 - [방화벽 설정 구성](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*.blob.core.usgovcloudapi.net |에이전트 통신 - [방화벽 설정 구성](../log-analytics/log-analytics-proxy-firewall.md) |

다음 Log Analytics 기능은 Azure Government에서 다르게 작동합니다.

* Azure Government의 [Log Analytics 포털](https://oms.microsoft.us)에서 Windows 에이전트를 다운로드해야 합니다.
* System Center Operations Manager 관리 서버를 Log Analytics에 연결하려면 업데이트된 관리 팩을 다운로드하고 가져와야 합니다.
  1. [업데이트된 관리 팩](http://go.microsoft.com/fwlink/?LinkId=828749)을 다운로드하고 저장합니다.
  2. 다운로드한 파일의 압축을 풉니다.
  3. 관리 팩을 Operations Manager로 가져옵니다. 디스크에서 관리 팩을 가져오는 방법에 대한 정보는 Microsoft TechNet 웹 사이트의 [Operations Manager 관리 팩을 가져오는 방법](http://technet.microsoft.com/library/hh212691.aspx) 토픽을 참조하세요.
  4. Operations Manager를 Log Analytics에 연결하려면 [Log Analytics에 Operations Manager 연결](../log-analytics/log-analytics-om-agents.md)의 단계를 따릅니다.

### <a name="frequently-asked-questions"></a>질문과 대답
* Microsoft Azure의 Log Analytics에서 Azure Government로 데이터를 마이그레이션할 수 있습니까?
  * 아니요. Microsoft Azure에서 Azure Government로 데이터 또는 작업 영역을 이동하는 것은 불가능합니다.
* Operations Management Suite Log Analytics 포털에서 Microsoft Azure와 Azure Government 작업 영역 간에 전환할 수 있습니까?
  * 아니요. Microsoft Azure 포털과 Azure Government 포털은 각각 별도이며 정보를 공유하지 않습니다.

자세한 내용은 [Log Analytics 공개 문서](../log-analytics/log-analytics-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트는 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.



<!--HONumber=Jan17_HO3-->


