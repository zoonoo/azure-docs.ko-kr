---
title: Azure 센티널에 Google 작업 영역 (G Suite) 데이터 연결 | Microsoft Docs
description: Google 작업 영역 (G Suite) 데이터 커넥터를 사용 하 여 Google Workspace 활동 이벤트를 Azure 센티널에 수집 하는 방법을 알아봅니다. 통합 문서에서 Google 작업 영역 데이터를 보고, 경고를 생성 하 고, 조사를 개선 합니다.
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
ms.openlocfilehash: 4ada570502d913283ba9ee4cc4c65b7bdd853935
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745262"
---
# <a name="connect-your-google-workspace-to-azure-sentinel"></a>Azure 센티널에 Google 작업 영역 연결

> [!IMPORTANT]
> Google 작업 영역 커넥터는 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

[Google 작업 영역 (이전의 G Suite)](https://workspace.google.com/) 데이터 커넥터는 REST API 통해 Google 작업 영역 활동 이벤트를 Azure 센티널에 수집 하는 기능을 제공 합니다. 커넥터는 SOC에서 이러한 [이벤트](https://developers.google.com/admin-sdk/reports/reference/rest/v1/activities) 표시 유형을 제공 하 여 잠재적인 보안 위험을 검사 하 고, 팀의 공동 작업 사용을 분석 하 고, 구성 문제를 분석 하 고, 로그인 및 시기를 추적 하 고, 관리자 작업을 분석 하 고, 사용자가 콘텐츠를 만들고 공유 하는 방법을 이해 하 고, 조직에서 더 많은 이벤트를 검토 하는

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

Google 작업 영역 데이터를 수집 하려면 다음 사용 권한 및 자격 증명이 있어야 합니다.

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한입니다.

- 작업 영역에 대 한 공유 키에 대 한 읽기 권한입니다. [작업 영역 키에 대해 자세히 알아보세요](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- 함수 앱를 만들 Azure Functions에 대 한 읽기 및 쓰기 권한입니다. [Azure Functions에 대해 자세히 알아보세요](../azure-functions/index.yml).

- REST API **GooglePickleString** 자격 증명이 필요 합니다. [Google REST API에 대해 자세히 알아보세요](https://developers.google.com/admin-sdk/reports/v1/reference/activities). [자격 증명을 얻는 방법에 대해 알아봅니다](https://developers.google.com/admin-sdk/reports/v1/quickstart/python).

## <a name="configure-and-connect-google-workspace"></a>Google 작업 영역 구성 및 연결

Google 작업 영역은 Azure 함수 앱를 사용 하 여 Azure 센티널에 직접 로그를 통합 하 고 내보낼 수 있습니다.

> [!NOTE]
> 이 커넥터는 Azure Functions를 사용 하 여 Google Reports API에 연결 하 여 활동 이벤트를 Azure 센티널로 끌어옵니다. 이로 인해 데이터 수집 비용이 추가로 발생할 수 있습니다. 자세한 내용은 [Azure Functions 가격 책정](https://azure.microsoft.com/pricing/details/functions/) 페이지를 확인 하세요.

1. Azure 센티널 포털에서 **데이터 커넥터** 를 클릭 합니다. 

1. 커넥터 갤러리에서 **Google 작업 영역 (G Suite) (미리 보기)** 을 선택 하 고 **커넥터 페이지 열기** 를 선택 합니다.

1. 커넥터 페이지의 **구성** 섹션에 설명 된 단계를 따릅니다.

## <a name="validate-connectivity-and-find-your-data"></a>연결 유효성 검사 및 데이터 찾기

Azure 센티널에서 수집 데이터를 볼 수 있을 때까지 최대 20 분이 걸릴 수 있습니다.

연결이 설정 되 면 데이터는 **로그** 의 **사용자 지정 로그** 섹션 아래에 있는 다음 표에 표시 됩니다.
- `GWorkspace_ReportsAPI_admin_CL`
- `GWorkspace_ReportsAPI_calendar_CL`
- `GWorkspace_ReportsAPI_drive_CL`
- `GWorkspace_ReportsAPI_login_CL`
- `GWorkspace_ReportsAPI_mobile_CL`
- `GWorkspace_ReportsAPI_token_CL`
- `GWorkspace_ReportsAPI_user_accounts_CL`

이 데이터 커넥터는 정상적으로 작동 하기 위해 Kusto 함수를 기반으로 하는 파서에 종속 됩니다. [다음 단계를 수행](https://aka.ms/sentinel-GWorkspaceReportsAPI-parser) 하 여 **GWorkspaceActivityReports** kusto 함수 별칭을 만듭니다.

몇 가지 유용한 샘플 쿼리는 커넥터 페이지의 **다음 단계** 탭을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 센티널에 Google 작업 영역을 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대 한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아보세요.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
