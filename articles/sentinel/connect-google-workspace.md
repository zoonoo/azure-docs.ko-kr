---
title: Azure Sentinel에 Google Workspace (G Suite) 데이터 연결 | Microsoft Docs
description: Google Workspace (G Suite) 데이터 커넥터를 사용하여 Google Workspace 활동 이벤트를 Azure Sentinel에 수집하는 방법을 알아봅니다. 통합 문서에서 Google Workspace 데이터를 보고, 경고를 만들고, 조사를 개선합니다.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 2ed49208a79436d3dd1bb2679129b0eafab39cca
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530454"
---
# <a name="connect-your-google-workspace-to-azure-sentinel"></a>Google Workspace를 Azure Sentinel에 연결

> [!IMPORTANT]
> Google Workspace 커넥터는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

[Google Workspace (이전의 G Suite)](https://workspace.google.com/) 데이터 커넥터는 REST API 통해 Google Workspace 활동 이벤트를 Azure Sentinel에 수집하는 기능을 제공합니다. 커넥터는 SOC에서 이러한 [이벤트](https://developers.google.com/admin-sdk/reports/reference/rest/v1/activities) 가시성을 제공하여 잠재적인 보안 위험을 검사하고, 팀의 공동 작업 내용을 분석하며, 구성 문제를 분석하고, 로그인 주체 및 시기를 추적하며, 관리자 작업을 분석하고, 사용자가 콘텐츠를 만들고 공유하는 방법을 이해하며, 조직에서 더 많은 이벤트를 검토하는 데 도움을 줍니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="prerequisites"></a>필수 구성 요소

Google Workspace 데이터를 수집하려면 다음 사용 권한 및 자격 증명이 있어야 합니다.

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한.

- 작업 영역 공유 키에 대한 읽기 권한. [작업 영역 키에 대해 자세히 알아보세요.](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)

- 함수 앱을 만들 Azure Functions에 대한 읽기 및 쓰기 권한. [Azure Functions에 대해 자세히 알아보세요.](../azure-functions/index.yml)

- REST API에는 **GooglePickleString** 자격 증명이 필요합니다. [Google REST API에 대해 자세히 알아보세요.](https://developers.google.com/admin-sdk/reports/v1/reference/activities) [자격 증명을 가져오는 방법을 알아보세요.](https://developers.google.com/admin-sdk/reports/v1/quickstart/python)

## <a name="configure-and-connect-google-workspace"></a>Google Workspace 구성 및 연결

Google Workspace를 통해 Azure Function App를 사용하여 Azure Sentinel에 직접 로그를 통합하고 내보낼 수 있습니다.

> [!NOTE]
> 이 커넥터는 Azure Functions를 사용하여 Google Reports API에 연결하여 활동 이벤트를 Azure Sentinel로 끌어옵니다. 이로 인해 데이터 수집 비용이 추가로 발생할 수 있습니다. 자세한 내용은 [Azure Functions 가격 책정 페이지](https://azure.microsoft.com/pricing/details/functions/)를 확인하세요.

1. Azure Sentinel 포털에서 **데이터 커넥터** 를 클릭합니다. 

1. 커넥터 갤러리에서 **Google Workspace (G Suite)(미리 보기)** 를 선택하고 **커넥터 페이지 열기** 를 선택합니다.

1. 커넥터 페이지의 **구성** 섹션에 설명된 단계를 따릅니다.

## <a name="validate-connectivity-and-find-your-data"></a>연결 유효성 검사 및 데이터 찾기

Azure Sentinel에서 수집 데이터를 볼 수 있을 때까지 최대 20분이 걸릴 수 있습니다.

연결이 설정되면 데이터는 다음 테이블의 **사용자 지정 로그** 섹션에 있는 **로그** 에 표시됩니다.
- `GWorkspace_ReportsAPI_admin_CL`
- `GWorkspace_ReportsAPI_calendar_CL`
- `GWorkspace_ReportsAPI_drive_CL`
- `GWorkspace_ReportsAPI_login_CL`
- `GWorkspace_ReportsAPI_mobile_CL`
- `GWorkspace_ReportsAPI_token_CL`
- `GWorkspace_ReportsAPI_user_accounts_CL`

이 데이터 커넥터는 정상적으로 작동하기 위해 Kusto 함수를 기반으로 하는 파서를 사용합니다. [다음 단계를 수행](https://aka.ms/sentinel-GWorkspaceReportsAPI-parser)하여 **GWorkspaceActivityReports** Kusto 함수 별칭을 만듭니다.

몇 가지 유용한 샘플 쿼리를 보려면 커넥터 페이지에서 **다음 단계** 탭을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Google Workspace를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.