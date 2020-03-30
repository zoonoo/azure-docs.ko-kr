---
title: Office 365 데이터를 Azure Sentinel에 연결 | 마이크로 소프트 문서
description: Office 365 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
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
ms.openlocfilehash: 43eba727b1dc724aae6eea3ec77de1363c5db73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252508"
---
# <a name="connect-data-from-office-365-logs"></a>Office 365 로그에서 데이터 연결



한 번의 클릭으로 [Office 365에서](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) Azure Sentinel로 감사 로그를 스트리밍할 수 있습니다. Office 365에서 동일한 테넌트의 Azure Sentinel 작업 영역으로 감사 로그를 스트리밍할 수 있습니다. Office 365 활동 로그 커넥터는 진행 중인 사용자 활동에 대한 통찰력을 제공합니다. Office 365에서 다양한 사용자, 관리자, 시스템 및 정책 작업 및 이벤트에 대한 정보를 얻을 수 있습니다. Office 365 로그를 Azure Sentinel에 연결하면 이 데이터를 사용하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사 프로세스를 개선할 수 있습니다.

> [!IMPORTANT]
> E3 라이선스가 있는 경우 Office 365 관리 활동 API를 통해 데이터에 액세스하려면 먼저 Office 365 조직에 대한 통합 감사 로깅을 사용하도록 설정해야 합니다. Office 365 감사 로그를 켜면 이 작업을 수행합니다. 지침은 Office [365 감사 로그 검색 켜기 또는 끄기](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)참조 자세한 내용은 [Office 365 관리 활동 API 참조를](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

- 테넌트의 글로벌 관리자 또는 보안 관리자여야 합니다.
- 테넌트는 통합 감사를 사용하도록 설정해야 합니다. Office 365 E3 또는 E5 라이선스가 있는 테넌트에는 기본적으로 통합 감사가 활성화되어 있습니다. <br>테넌트에 이러한 라이선스가 없는 경우 다음 방법 중 하나를 사용하여 테넌트에 대한 통합 감사를 사용하도록 설정해야 합니다.
    - [설정-관리감사LogLogConfig cmdlet을 사용하고](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) 매개 변수 "통합 감사로깅활성화"를 사용하도록 설정합니다.
    - [보안 & 준수 센터 UI사용.](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin)

## <a name="connect-to-office-365"></a>Office 365에 연결

1. Azure Sentinel에서 데이터 커넥터를 선택한 다음 **Office 365** 타일을 **클릭합니다.**

2. 아직 활성화하지 않은 경우 데이터 커넥터 블레이드로 이동하여 **Office 365** **커넥터를** 선택하여 활성화할 수 있습니다. 여기에서 **커넥터 열기 페이지를** 클릭하고 구성 섹션에 레이블이 지정된 **구성** 섹션에서 Azure Sentinel에 연결하려는 모든 Office 365 활동 로그를 선택할 수 있습니다. 
   > [!NOTE]
   > Azure Sentinel에서 이전에 지원되었던 Office 365 커넥터 버전에서 여러 테넌트를 이미 연결한 경우 각 테넌트에서 수집한 로그를 보고 수정할 수 있습니다. 테넌을 추가할 수는 없지만 이전에 추가한 테넌트로는 제거할 수 있습니다.
3. Office 365 로그에 대한 로그 분석에서 관련 스키마를 사용하려면 **OfficeActivity**를 검색합니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Office 365를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

