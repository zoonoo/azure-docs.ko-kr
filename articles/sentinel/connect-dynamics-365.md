---
title: Dynamics 365 로그를 Azure 센티널에 연결 | Microsoft Docs
description: Dynamics 365 Common Data Service (CD) 작업 커넥터를 사용 하 여 진행 중인 관리자, 사용자 및 지원 활동에 대 한 정보를 가져오는 방법에 대해 알아봅니다.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: 018368b6284cf39edec01f0a9a943b8ea15c85d0
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98104183"
---
# <a name="connect-dynamics-365-activity-logs-to-azure-sentinel"></a>Dynamics 365 활동 로그를 Azure 센티널에 연결

[Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) COMMON DATA SERVICE (cd) 활동 커넥터는 관리자, 사용자 및 지원 활동 뿐만 아니라 Microsoft 소셜 Engagement 로깅 이벤트에 대 한 통찰력을 제공 합니다. Dynamics 365 CRM 로그를 Azure 센티널에 연결 하면 통합 문서에서이 데이터를 보고,이 데이터를 사용 하 여 사용자 지정 경고를 만들고,이를 활용 하 여 조사 프로세스를 개선할 수 있습니다. 이 새 Azure 센티널 커넥터는 Office 관리 API에서 Dynamics CD 데이터를 수집 합니다. Power Platform에서 감사 된 Dynamics CD 활동에 대해 자세히 알아보려면 [활동 로깅 사용 및 사용](/power-platform/admin/enable-use-comprehensive-auditing)을 참조 하세요.

> [!IMPORTANT]
>
> Dynamics 365 Common Data Service (CD) 작업 커넥터는 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

## <a name="prerequisites"></a>사전 준비 사항

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

- [Microsoft Dynamics 365 프로덕션 라이선스가](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description)있어야 합니다. 이 커넥터는 샌드박스 환경에서 사용할 수 없습니다.
    - 활동 로깅을 수행 하려면 Microsoft 365 Enterprise [E3 또는 E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) 구독이 필요 합니다.

- Office 관리 API에서 데이터를 꺼내려면:
    - 테 넌 트의 전역 관리자 여야 합니다.

    - Office [보안 및 규정 준수 센터](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance)에서 [office 감사 로깅을](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) 사용 하도록 설정 해야 합니다.

## <a name="enable-the-dynamics-365-activities-data-connector"></a>Dynamics 365 활동 데이터 커넥터를 사용 하도록 설정

1. Azure 센티널 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. **데이터 커넥터** 갤러리에서 **Dynamics 365 (미리 보기)** 를 선택한 다음 미리 보기 창에서 **커넥터 페이지 열기** 를 선택 합니다.

1. **지침** 탭의 **구성** 에서 **연결** 을 클릭 합니다. 

    커넥터를 활성화 한 후에는 그래프에 도착 한 데이터를 볼 수 있을 때까지 약 30 분 정도가 소요 됩니다. 

    [규정 준수 센터의 Office 감사 로그](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#requirements-to-search-the-audit-log)에 따라 결과에 해당 감사 로그 레코드가 반환 될 때까지 최대 30 분 또는 최대 24 시간이 걸릴 수 있습니다.

1. 테이블에서 Microsoft Dynamics 감사 로그를 찾을 수 있습니다 `Dynamics365Activity` . 테이블의 [스키마 참조](/azure/azure-monitor/reference/tables/dynamics365activity)를 참조 하세요.

## <a name="querying-the-data"></a>데이터 쿼리

1. Azure 센티널 탐색 메뉴에서 **로그** 를 선택 합니다.

1. 다음 쿼리를 실행 하 여 로그가 도착 했는지 확인 합니다.

    ```kusto
    Dynamics365Activity
    | take 10
    ```


## <a name="next-steps"></a>다음 단계
이 문서에서는 Dynamics 365 활동 데이터를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아보세요.
- [기본 제공](tutorial-detect-threats-built-in.md) 또는 [사용자 지정](tutorial-detect-threats-custom.md) 규칙을 사용 하 여 Azure 센티널에서 위협을 검색 하기 시작 합니다.
