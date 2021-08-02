---
title: Azure Sentinel에 Office 365 로그 연결| Microsoft Docs
description: Office 365 로그 커넥터를 사용하여 OneDrive를 포함한 Exchange, Teams 및 SharePoint에서 진행 중인 사용자 및 관리자 활동에 대한 정보를 가져오는 방법을 알아봅니다.
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
ms.openlocfilehash: 135ca977f19ed44742b1086c7a02a9b89dab1445
ms.sourcegitcommit: ef950cf37f65ea7a0f583e246cfbf13f1913eb12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111422033"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Azure Sentinel에 Office 365 로그 연결

[Office 365](/office/) 로그 커넥터는 **Exchange**, **SharePoint**(**OneDrive** 포함) 및 **Teams** 에서 진행 중인 사용자 및 관리자 활동에 대한 Azure Sentinel 정보를 제공합니다. 이 정보에는 작업을 수행한 사용자의 세부 정보뿐만 아니라 파일 다운로드, 보낸 액세스 요청, 그룹 이벤트 변경 내용, 사서함 작업, 팀 이벤트(예: 채팅, 팀, 멤버 및 채널 이벤트)와 같은 작업에 대한 세부 정보가 포함됩니다. Office 365 로그를 Azure Sentinel에 연결하면 통합 문서에서 이 데이터를 보고 분석하고, 사용자 지정 경고를 만들어 쿼리하고, 이를 통합하여 조사 프로세스를 개선하고, Office 365 보안에 대한 더 많은 인사이트를 얻을 수 있습니다.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>필수 구성 요소

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- 테넌트의 전역 관리자 또는 보안 관리자여야 합니다.

- Office 365 배포는 Azure Sentinel 작업 영역과 동일한 테넌트에 있어야 합니다.

> [!IMPORTANT]
> - 커넥터가 Office 365 관리 활동 API를 통해 데이터에 액세스할 수 있도록 하려면 Office 365 배포에서 **통합 감사 로깅** 을 사용하도록 설정해야 합니다. 보유하고 있는 Office 365/Microsoft 365 라이선스 유형에 따라 기본적으로 활성화되거나 활성화되지 않을 수 있습니다. 라이선스 유형에 따라 통합 감사 로깅의 상태를 확인하려면 [Office 365 보안 및 규정 준수 센터](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)에 문의하세요.
> - Office 365 통합 감사 로깅의 현재 상태를 수동으로 활성화, 비활성화 및 확인할 수도 있습니다. 지침은 [Office 365 감사 로그 검색 설정 또는 해제](/office365/securitycompliance/turn-audit-log-search-on-or-off)를 참조하세요.
> - 자세한 내용은 [Office 365 관리 작업 API 참조](/office/office-365-management-api/office-365-management-activity-api-reference)를 참조하세요.


   > [!NOTE]
   > 위에서 언급한 것처럼 **데이터 형식** 아래의 커넥터 페이지에서 볼 수 있듯이 Azure Sentinel Office 365 커넥터는 현재 Microsoft Exchange, SharePoint(OneDrive 포함) 및 Teams에서만 감사 로그 수집을 지원합니다. 그러나 [다른 Office 데이터](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888)를 Azure Sentinel로 가져오는 데 관심이 있는 경우에는 몇 가지 외부 솔루션이 있습니다. 

## <a name="enable-the-office-365-log-connector"></a>Office 365 로그 커넥터 사용

### <a name="instructions-tab"></a>지침 탭

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. **데이터 커넥터** 갤러리에서 **Office 365** 를 선택한 다음, 미리 보기 창에서 **커넥터 페이지 열기** 를 선택합니다.

1. **구성** 섹션 아래에서 Azure Sentinel에 연결하려는 Office 365 활동 로그의 확인란을 선택하고 **변경 내용 적용** 을 클릭합니다. 

   > [!NOTE]
   > 이전에 이를 지원되던 이전 버전의 Office 365 커넥터를 사용하여 Azure Sentinel에 여러 테넌트를 연결한 경우 각 테넌트에서 수집하는 로그를 보고 수정할 수 있습니다. 다른 테넌트를 추가할 수는 없지만 이전에 추가된 테넌트를 제거할 수는 있습니다.

### <a name="next-steps-tab"></a>다음 단계 탭

- SharePoint, OneDrive, Exchange 및 Teams 로그 데이터에 대한 인사이트를 가져오려면 **Office 365** 로그 커넥터와 함께 제공되는 권장 통합 문서, 쿼리 샘플 및 분석 규칙 템플릿을 참조하세요.

- **로그** 에서 Office 365 로그 데이터를 수동으로 쿼리하려면 쿼리 창의 첫 번째 줄에 `OfficeActivity`를 입력합니다.
   - 특정 로그 유형에 대한 쿼리를 필터링하려면 쿼리의 두 번째 줄에 `| where OfficeWorkload == "<logtype>"`을 입력합니다. 여기서 *\<logtype\>* 는 `SharePoint`, `OneDrive`, `Exchange` 또는 `MicrosoftTeams`입니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Office 365를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [기본 제공](tutorial-detect-threats-built-in.md) 또는 [사용자 지정](tutorial-detect-threats-custom.md) 규칙을 사용하여 Azure Sentinel에서 위협 검색을 시작합니다.