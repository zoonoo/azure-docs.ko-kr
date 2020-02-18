---
title: Azure 센티널에 Office 365 데이터 연결 | Microsoft Docs
description: Azure 센티널에 Office 365 데이터를 연결 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2020
ms.author: rkarlin
ms.openlocfilehash: 709961fe63e5ed862a0e8dc3fa735d426dd02998
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371323"
---
# <a name="connect-data-from-office-365-logs"></a>Office 365 로그의 데이터 연결



한 번의 클릭으로 [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) 의 감사 로그를 Azure 센티널로 스트리밍할 수 있습니다. Office 365의 감사 로그를 동일한 테 넌 트의 Azure 센티널 작업 영역으로 스트리밍할 수 있습니다. Office 365 활동 로그 커넥터는 지속적인 사용자 활동에 대 한 통찰력을 제공 합니다. Office 365에서 다양 한 사용자, 관리자, 시스템 및 정책 작업 및 이벤트에 대 한 정보를 얻을 수 있습니다. Office 365 로그를 Azure 센티널에 연결 하면이 데이터를 사용 하 여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사 프로세스를 개선할 수 있습니다.

> [!IMPORTANT]
> E3 라이선스가 있는 경우 Office 365 관리 활동 API를 통해 데이터에 액세스 하려면 먼저 Office 365 조직에 대해 통합 된 감사 로깅을 사용 하도록 설정 해야 합니다. 이렇게 하려면 Office 365 감사 로그를 설정 합니다. 지침은 [Office 365 감사 로그 검색 설정 또는 해제](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)를 참조 하세요. 자세한 내용은 [Office 365 관리 활동 API 참조](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 테 넌 트의 전역 관리자 또는 보안 관리자 여야 합니다.
- 테 넌 트에 Office 365 E3 또는 Office 365 E5 라이선스가 없는 경우 다음 프로세스 중 하나를 사용 하 여 테 넌 트에서 통합 감사를 사용 하도록 설정 해야 합니다.
    - [Set AdminAuditLogConfig cmdlet을 사용 하](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) 고 "UnifiedAuditLogIngestionEnabled" 매개 변수를 사용 하도록 설정 합니다.
    - [또는 보안 및 준수 센터 UI를 사용](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin)합니다.

## <a name="connect-to-office-365"></a>Office 365에 연결

1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음 **Office 365** 타일을 클릭 합니다.

2. 아직 사용 하도록 설정 하지 않은 경우 **데이터 커넥터** 블레이드로 이동 하 고 **Office 365** 커넥터를 선택 하 여 수행할 수 있습니다. 여기에서 **커넥터 열기 페이지** 를 클릭 하 고 구성 섹션 **구성** 섹션에서 Azure 센티널에 연결 하려는 모든 Office 365 활동 로그를 선택 합니다. 
   > [!NOTE]
   > Azure 센티널에서 이전에 지원 되 던 Office 365 커넥터 버전에서 여러 테 넌 트를 이미 연결한 경우 각 테 넌 트에서 수집 하는 로그를 보고 수정할 수 있습니다. 다른 테 넌 트를 추가할 수는 없지만 이전에 추가 된 테 넌 트를 제거할 수는 있습니다.
3. Office 365 로그에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **작업**을 검색 합니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Office 365을 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

