---
title: Azure Government 설명서 | Microsoft Docs
description: Azure Government용 응용 프로그램 개발에 대한 지침 및 기능 비교를 제공합니다.
services: Azure-Government
cloud: gov
documentationcenter: ''
author: scooxl
manager: zakramer
editor: ''

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/05/2016
ms.author: scooxl

---
# <a name="azure-government-management-and-security"></a>Azure Government 관리 및 보안
## <a name="key-vault"></a>키 자격 증명 모음
이 서비스에 대한 자세한 내용 및 사용 방법은 <a href="https://azure.microsoft.com/documentation/services/key-vault">Azure Key Vault 공개 문서</a>를 참조하세요.

### <a name="data-considerations"></a>데이터 고려 사항
다음 정보는 Azure 주요 자격 증명 모음에 대한 Azure Government 경계를 식별합니다.

| 규제된/제어된 데이터 허용됨 | 규제된/제어된 데이터 허용되지 않음 |
| --- | --- |
| Azure 주요 자격 증명 모음으로 암호화된 모든 데이터는 규제된/제어된 데이터를 포함할 수 있습니다. |Azure 주요 자격 증명 모음 메타데이터는 내보내기 제어된 데이터를 포함하도록 허용되지 않습니다. 이 메타데이터는 주요 자격 증명 모음을 생성 및 유지 관리하는 경우 입력된 모든 구성 데이터를 포함합니다.  다음 필드에 규제된/제어된 데이터인 리소스 그룹 이름, 주요 자격 증명 모음 이름, 구독 이름을 입력하지 마세요. |

주요 자격 증명 모음은 일반적으로 Azure Government에서 사용할 수 있습니다. 공용에서 마찬가지로 확장명이 없으므로 PowerShell 및 CLI를 통해서만 주요 자격 증명 모음을 사용할 수 있습니다.

## <a name="log-analytics"></a>Log Analytics
Log Analytics는 일반적으로 Azure Government에서 사용할 수 있습니다. 

### <a name="differences-from-public-azure"></a>공용 Azure와의 차이점
다음 Log Analytics 기능과 솔루션은 현재 Azure Government에서 사용할 수 없습니다. 이 목록은 기능/솔루션 상태가 변경될 때 업데이트됩니다.

* 공용 Azure에서 미리 보기에 있는 솔루션은 다음을 포함합니다.
  * 네트워크 모니터링 솔루션
  * Azure 네트워킹 분석 솔루션
  * Office 365 솔루션
  * Windows 10 Analytics 업그레이드 솔루션
  * 응용 프로그램 종속성 모니터링
  * Application Insights
  * Azure 활동 로그
  * Azure Automation 분석
  * Key Vault 분석
* Azure Automation에 필요한 솔루션 및 기능은 다음을 포함합니다.
  * 업데이트 관리
  * 변경 관리
  * Azure Automation Runbook을 트리거하는 경고
* 온-프레미스 소프트웨어에 대한 업데이트를 필요로 하는 솔루션 및 기능은 다음을 포함합니다.
  * System Center Operations Manager 2016과 통합
  * System Center Configuration Manager의 컴퓨터 그룹
  * Surface Hub 솔루션
* 공용 Azure에서 미리 보기에 있는 기능은 다음을 포함합니다.
  * PowerBI로 데이터 내보내기
* Azure Portal 통합
  * 모니터링할 Azure Storage는 반드시 PowerShell 또는 Resource Manager 템플릿을 통해 선택해야 함
  * Log Analytics를 활성화할 가상 컴퓨터는 반드시 PowerShell 또는 Resource Manager 템플릿을 통해 선택해야 함
  * Azure 메트릭 및 Azure 진단
* OMS 모바일 응용 프로그램
* OMS Linux Agent VM 확장
* 사용 데이터

다음 Log Analytics 기능은 Azure Government에서 다르게 작동합니다.

* Azure Government의 [Log Analytics 포털](https://oms.microsoft.us)에서 Windows 에이전트를 다운로드해야 합니다.
* 데이터 수집기 API를 사용하는 데이터 업로드는 Azure Government URL, https://*workspaceId*.ods.opinsights.azure.us의 사용이 필요합니다. 여기서 *workspaceId*는 OMS 포털에서 작업 영역 ID입니다. 
* System Center Operations Manager 관리 서버를 Log Analytics에 연결하려면 업데이트된 관리 팩을 다운로드하고 가져와야 합니다.
  1. [업데이트된 관리 팩](http://go.microsoft.com/fwlink/?LinkId=828749)을 다운로드하고 저장합니다.
  2. 다운로드한 파일의 압축을 풉니다.
  3. 관리 팩을 Operations Manager로 가져옵니다. 디스크에서 관리 팩을 가져오는 방법에 대한 정보는 Microsoft TechNet 웹 사이트의 [Operations Manager 관리 팩을 가져오는 방법](http://technet.microsoft.com/library/hh212691.aspx) 항목을 참조하세요.
  4. Operations Manager를 Log Analytics에 연결하려면 [Log Analytics에 Operations Manager 연결](../log-analytics/log-analytics-om-agents.md)의 단계를 따릅니다. 

### <a name="frequently-asked-questions"></a>질문과 대답
* 공용 Azure의 Log Analytics에서 Azure Government로 데이터를 마이그레이션할 수 있습니까?
  * 아니요. 공용 Azure에서 Azure Government로 데이터 또는 작업 영역을 이동하는 것은 불가능합니다.
* OMS Log Analytics 포털에서 공용 Azure와 Azure Government 작업 영역 간에 전환할 수 있습니까?
  * 아니요. 공용 Azure 포털과 Azure Government 포털은 각각 별도이며 정보를 공유하지 않습니다. 

자세한 내용은 [Log Analytics 공개 문서](../log-analytics/log-analytics-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트는 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.

<!--HONumber=Oct16_HO2-->


