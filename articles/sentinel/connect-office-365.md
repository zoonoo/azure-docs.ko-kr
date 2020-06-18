---
title: Azure Sentinel에 Office 365 데이터 연결| Microsoft Docs
description: Azure Sentinel에 Office 365 데이터를 연결하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2020
ms.author: yelevin
ms.openlocfilehash: c3e63063b3ea4e7fba3997ddd645aa59fe857488
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758574"
---
# <a name="connect-data-from-office-365-logs"></a>Office 365 로그의 데이터 연결



한 번의 클릭으로 [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide)의 감사 로그를 Azure Sentinel로 스트리밍할 수 있습니다. 동일한 테넌트에서 Office 365의 감사 로그를 Azure Sentinel 작업 영역으로 스트리밍할 수 있습니다. Office 365 활동 로그 커넥터는 지속적인 사용자 활동에 대한 인사이트를 제공합니다. Office 365에서 다양한 사용자, 관리자, 시스템, 정책 작업 및 이벤트에 대한 정보를 얻을 수 있습니다. Office 365 로그를 Azure Sentinel에 연결하면 이 데이터를 사용하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사 프로세스를 개선할 수 있습니다.

> [!IMPORTANT]
> E3 라이선스가 있는 경우 Office 365 관리 작업 API를 통해 데이터에 액세스하려면 먼저 Office 365 조직에 대해 통합된 감사 로깅을 사용하도록 설정해야 합니다. Office 365 감사 로그를 설정하여 이 작업을 수행합니다. 지침은 [Office 365 감사 로그 검색 설정 또는 해제](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)를 참조하세요. 자세한 내용은 [Office 365 관리 작업 API 참조](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 테넌트의 전역 관리자 또는 보안 관리자여야 합니다.
- 테넌트가 통합 감사를 사용해야 합니다. Office 365 E3 또는 E5 라이선스를 사용하는 테넌트는 기본적으로 통합 감사를 사용합니다. <br>테넌트에 이러한 라이선스 중 하나가 없는 경우 다음 방법 중 하나를 사용하여 테넌트에서 통합 감사를 사용하도록 설정해야 합니다.
    - [Set-AdminAuditLogConfig cmdlet을 사용](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps)하고 매개 변수 “UnifiedAuditLogIngestionEnabled”를 사용하도록 설정합니다.
    - [보안 및 규정 센터 UI 사용](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).
   
   > [!NOTE]
   > 현재 O365 데이터 커넥터는 데이터 형식 섹션의 커넥터 페이지에 설명된 대로 Exchange 및 Sharepoint 활동만 자동으로 캡처합니다. [Teams 감사 데이터가 필요하고 Sentinel을 사용하여 Teams를 보호하는 경우 이 문서](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761)를 확인하는 것이 좋습니다. 

## <a name="connect-to-office-365"></a>Office 365에 연결

1. Azure Sentinel에서 **데이터 커넥터**를 선택한 다음 **Office 365** 타일을 클릭합니다.

2. 아직 사용하도록 설정하지 않은 경우 **데이터 커넥터** 블레이드로 이동하고 **Office 365** 커넥터를 선택하여 이 작업을 수행할 수 있습니다. 여기에서 **커넥터 페이지 열기**를 클릭하고 **구성**이라고 레이블이 지정된 구성 섹션에서 Azure Sentinel에 연결할 모든 Office 365 활동 로그를 선택할 수 있습니다. 
   > [!NOTE]
   > Azure Sentinel의 이전에 지원되던 Office 365 커넥터 버전에서 여러 테넌트를 이미 연결한 경우 각 테넌트에서 수집하는 로그를 보고 수정할 수 있습니다. 다른 테넌트를 추가할 수는 없지만 이전에 추가된 테넌트를 제거할 수는 있습니다.
3. Office 365 로그의 Log Analytics에서 관련 스키마를 사용하려면 **OfficeActivity**를 검색합니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Office 365를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

