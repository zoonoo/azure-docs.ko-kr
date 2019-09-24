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
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 481acfdccb54077f18c1f4bf0375c0dc66e66007
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240655"
---
# <a name="connect-data-from-office-365-logs"></a>Office 365 로그의 데이터 연결



한 번의 클릭으로 [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) 의 감사 로그를 Azure 센티널로 스트리밍할 수 있습니다. 여러 테 넌 트의 감사 로그를 Azure 센티널의 단일 작업 영역으로 스트리밍할 수 있습니다. Office 365 활동 로그 커넥터는 지속적인 사용자 활동에 대 한 통찰력을 제공 합니다. Office 365에서 다양 한 사용자, 관리자, 시스템 및 정책 작업 및 이벤트에 대 한 정보를 얻을 수 있습니다. Office 365 로그를 Azure 센티널에 연결 하면이 데이터를 사용 하 여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사 프로세스를 개선할 수 있습니다.

> [!IMPORTANT]
> E3 라이선스가 있는 경우 Office 365 관리 활동 API를 통해 데이터에 액세스 하려면 먼저 Office 365 조직에 대해 통합 된 감사 로깅을 사용 하도록 설정 해야 합니다. 이렇게 하려면 Office 365 감사 로그를 설정 합니다. 지침은 [Office 365 감사 로그 검색 설정 또는 해제](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)를 참조 하세요. 자세한 내용은 [Office 365 관리 활동 API 참조](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 테 넌 트의 전역 관리자 또는 보안 관리자 여야 합니다.
- Azure 센티널에 로그인 하 여 연결을 만드는 컴퓨터에서 포트 4433이 웹 트래픽에 대해 열려 있는지 확인 합니다. 연결이 설정 되 면이 포트를 다시 닫을 수 있습니다.
- 테 넌 트에 Office 365 E3 또는 Office 365 E5 라이선스가 없는 경우 다음 프로세스 중 하나를 사용 하 여 테 넌 트에서 통합 감사를 사용 하도록 설정 해야 합니다.
    - [Set AdminAuditLogConfig cmdlet을 사용 하](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) 고 "UnifiedAuditLogIngestionEnabled" 매개 변수를 사용 하도록 설정 합니다.
    - [또는 보안 및 준수 센터 UI를 사용](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin)합니다.

## <a name="connect-to-office-365"></a>Office 365에 연결

1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음 **Office 365** 타일을 클릭 합니다.

2. 아직 사용 하도록 설정 하지 않은 경우 **연결** 에서 사용 단추를 사용 하 여 Office 365 솔루션을 사용 하도록 **설정 합니다.** 이미 사용 하도록 설정 된 경우 연결 화면에서 이미 사용 하도록 설정 된 것으로 식별 됩니다.
1. Office 365에서는 여러 테 넌 트의 데이터를 Azure 센티널로 스트리밍할 수 있습니다. 연결 하려는 각 테 넌 트에 대해 **Azure 센티널에**테 넌 트 연결 아래에 테 넌 트를 추가 합니다. 
1. Active Directory 화면이 열립니다. Azure 센티널에 연결 하려는 각 테 넌 트에서 전역 관리자에 게 인증 하 고 해당 로그를 읽을 수 있도록 Azure 센티널에 사용 권한을 제공 하 라는 메시지가 표시 됩니다. 
5. Stream Office 365 활동 로그에서 **선택** 을 클릭 하 여 Azure 센티널로 스트리밍할 로그 유형을 선택 합니다. 현재 Azure 센티널은 Exchange와 SharePoint를 지원 합니다.

4. **변경 내용 적용**을 클릭 합니다.

3. Office 365 로그에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **작업**을 검색 합니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Office 365을 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 얻는](quickstart-get-visibility.md)방법에 대해 알아봅니다.
- [Azure 센티널로 위협 검색을](tutorial-detect-threats-built-in.md)시작 합니다.

