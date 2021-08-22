---
title: Dynamics 365 로그를 Azure Sentinel에 연결 | Microsoft Docs
description: Dynamics 365 CDS(Common Data Service) 활동 커넥터를 사용하여 진행 중인 관리자, 사용자 및 지원 활동에 대한 정보를 가져오는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/03/2021
ms.author: yelevin
ms.openlocfilehash: d8151ac8f0ae26bc3567ec3a2a533703b401a90f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529195"
---
# <a name="connect-dynamics-365-activity-logs-to-azure-sentinel"></a>Dynamics 365 활동 로그를 Azure Sentinel에 연결

[Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) CDS(Common Data Service) 활동 커넥터는 관리자, 사용자 및 지원 활동과 Microsoft Social Engagement 로깅 이벤트에 관한 인사이트를 제공합니다. Dynamics 365 CRM 로그를 Azure Sentinel에 연결하면 통합 문서에서 이 데이터를 보고 이를 통해 사용자 지정 경고를 만들어 조사 프로세스를 개선할 수 있습니다. 이 새 Azure Sentinel 커넥터는 Office 관리 API에서 Dynamics CDS 데이터를 수집합니다. Power Platform에서 감사된 Dynamics CD 활동에 대해 자세히 알아보려면 [활동 로깅 활성화 및 사용](/power-platform/admin/enable-use-comprehensive-auditing)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>사전 요구 사항

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- [Microsoft Dynamics 365 프로덕션 라이선스](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description)가 있어야 합니다. 이 커넥터는 샌드박스 환경에서 사용할 수 없습니다.
    - 활동 로깅을 수행하려면 Microsoft 365 Enterprise [E3 또는 E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) 구독이 필요합니다.

- Office 관리 API에서 데이터를 풀하려면:
    - 테넌트의 전역 관리자여야 합니다.

    - [Office 감사 로깅](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)은 [Office 보안 및 규정 준수 센터](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance)에서 사용하도록 설정해야 합니다.

## <a name="enable-the-dynamics-365-activities-data-connector"></a>Dynamics 365 활동 데이터 커넥터 사용

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. **데이터 커넥터** 갤러리에서 **Dynamics 365** 를 선택한 다음, 미리 보기 창에서 **커넥터 페이지 열기** 를 선택합니다.

1. **지침** 탭의 **구성** 에서 **연결** 을 클릭합니다. 

    커넥터를 활성화한 후에는 그래프에 도착한 데이터를 볼 수 있을 때까지 약 30분 정도가 소요됩니다. 

    [준수 센터의 Office 감사 로그](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#requirements-to-search-the-audit-log)에 따르면 이벤트 발생 후 해당 감사 로그 레코드가 결과에 반환되는 데 최대 30분에서 24시간이 걸릴 수 있습니다.

1. `Dynamics365Activity` 테이블에서 Microsoft Dynamics 감사 로그를 찾을 수 있습니다. 테이블의 [스키마 참조](/azure/azure-monitor/reference/tables/dynamics365activity)를 참조하세요.

## <a name="querying-the-data"></a>데이터 쿼리

1. Azure Sentinel 탐색 메뉴에서 **로그** 를 선택합니다.

1. 다음 쿼리를 실행하여 로그가 도착했는지 확인합니다.

    ```kusto
    Dynamics365Activity
    | take 10
    ```


## <a name="next-steps"></a>다음 단계
이 문서에서는 Dynamics 365 활동 데이터를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [기본 제공](detect-threats-built-in.md) 또는 [사용자 지정](detect-threats-custom.md) 규칙을 사용하여 Azure Sentinel에서 위협 검색을 시작합니다.
