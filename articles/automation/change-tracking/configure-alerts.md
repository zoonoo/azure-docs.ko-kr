---
title: Azure Automation 변경 내용 추적 및 인벤토리에 대 한 경고를 만드는 방법
description: 이 문서에서는 변경 내용 추적 및 인벤토리에 의해 검색 된 변경의 상태에 대해 알리도록 Azure alerts를 구성 하는 방법을 설명 합니다.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/15/2020
ms.topic: conceptual
ms.openlocfilehash: 275e57e5dcf173e8d5f30f262641b02698910422
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210085"
---
# <a name="how-to-create-alerts-for-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리에 대 한 경고를 만드는 방법

Azure의 경고는 runbook 작업, 서비스 상태 문제 또는 Automation 계정과 관련 된 기타 시나리오의 결과를 사전에 알려 줍니다. Azure Automation에는 미리 구성 된 경고 규칙이 포함 되지 않지만 생성 되는 데이터를 기반으로 사용자 고유의 사용자를 만들 수 있습니다. 이 문서에서는 변경 내용 추적 및 인벤토리에 의해 식별 되는 변경 내용에 따라 경고 규칙을 만드는 방법에 대 한 지침을 제공 합니다.

Azure Monitor 경고에 익숙하지 않은 경우 시작 하기 전에 [Microsoft Azure의 경고 개요](../../azure-monitor/platform/alerts-overview.md) 를 참조 하세요. 로그 쿼리를 사용 하는 경고에 대해 자세히 알아보려면 [Azure Monitor의 로그 경고](../../azure-monitor/platform/alerts-unified-log.md)를 참조 하세요.

## <a name="create-alert"></a>경고 만들기

다음 예제에서는 **c:\windows\system32\drivers\etc\hosts** 파일이 컴퓨터에서 수정 된 것을 보여 줍니다. 이 파일은 Windows가 호스트 이름을 IP 주소로 확인하는 데 사용하기 때문에 중요합니다. 이 작업은 DNS보다 우선하므로 연결 문제가 발생할 수 있습니다. 악의적인 또는 위험한 웹 사이트로 트래픽 리디렉션이 발생할 수도 있습니다.

![호스트 파일 변경을 보여 주는 차트](./media/configure-alerts/changes.png)

이 예제를 사용하여 변경에 대한 경고를 만드는 단계를 설명하겠습니다.

1. Automation 계정에서 **변경 내용 추적** 페이지에서 **Log Analytics**를 선택 합니다.

2. 로그 검색에서 `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` 쿼리를 사용하여 **hosts** 파일의 콘텐츠 변경 내용을 찾습니다. 이 쿼리는 단어를 포함 하는 정규화 된 경로 이름으로 파일에 대 한 콘텐츠 변경 내용을 찾습니다 `hosts` . 경로 부분을 정규화된 형식으로 변경하여(예: `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`를 사용) 특정 파일을 요청할 수도 있습니다.

3. 쿼리가 결과를 반환한 후 로그 검색에서 **새 경고 규칙** 을 선택 하 여 **경고 생성** 페이지를 엽니다. Azure Portal에서 **Azure Monitor**를 통해 이 페이지로 이동할 수도 있습니다.

4. 쿼리를 다시 확인하고 경고 논리를 수정합니다. 여기서는 환경의 모든 컴퓨터에서 변경 내용이 여러 개 검색되면 경고를 트리거할 수 있습니다.

    ![hosts 파일 변경 내용을 추적하기 위한 쿼리의 변경](./media/configure-alerts/change-query.png)

5. 경고 논리를 설정한 후에는 경고의 트리거에 대 한 응답으로 작업을 수행 하는 작업 그룹을 할당 합니다. 이 경우 이메일을 보내도록 설정하고 ITSM(IT 서비스 관리) 티켓을 만듭니다.

업데이트 배포 상태를 확인할 수 있도록 경고를 설정 하려면 다음 단계를 수행 합니다. Azure alerts를 처음 접하는 경우 [Azure alerts 개요](../../azure-monitor/platform/alerts-overview.md)를 참조 하세요.

## <a name="configure-action-groups-for-your-alerts"></a>경고에 대한 작업 그룹 구성

경고가 구성되었으면 여러 경고에서 사용할 작업 그룹을 설정할 수 있습니다. 작업에는 전자 메일 알림, runbook, 웹 후크 등이 포함 될 수 있습니다. 작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](../../azure-monitor/platform/action-groups.md)를 참조하세요.

1. 경고를 선택한 다음, **작업 그룹**에서 **새로 만들기**를 선택합니다.

2. 작업 그룹의 전체 이름과 약식 이름을 입력합니다. 업데이트 관리는 지정된 그룹을 사용하여 알림을 보낼 때 약식 이름을 사용합니다.

3. **작업**에서 작업을 지정하는 이름(예: **이메일 알림**)을 입력합니다.

4. **작업 유형**에서 적절한 유형(예: **이메일/SMS/푸시/음성**)을 선택합니다.

5. **세부 정보 편집**을 선택합니다.

6. 작업 유형에 대한 창을 채웁니다. 예를 들어 **email/SMS/Push/Voice**를 사용 하는 경우 작업 이름을 입력 하 고, **메일** 확인란을 선택 하 고, 유효한 전자 메일 주소를 입력 한 다음, **확인**을 선택 합니다.

    ![이메일 작업 그룹 구성](./media/configure-alerts/configure-email-action-group.png)

7. 작업 그룹 추가 창에서 **확인**을 선택합니다.

8. 경고 이메일의 경우 이메일 제목을 사용자 지정할 수 있습니다. **규칙 만들기**에서 **작업 사용자 지정**을 선택한 다음, **이메일 제목**을 선택합니다.

9. 작업이 완료되면 **경고 규칙 만들기**를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Monitor의 경고](../../azure-monitor/platform/alerts-overview.md)에 대해 자세히 알아보세요.

* Log Analytics 작업 영역에서 데이터를 검색 하 고 분석 하는 [로그 쿼리에](../../azure-monitor/log-query/log-query-overview.md) 대해 알아봅니다.

* [Azure Monitor 로그를 사용 하 여 사용량 및 비용](../../azure-monitor/platform/manage-cost-storage.md) 관리 데이터 보존 기간을 변경 하 여 비용을 제어 하는 방법 및 데이터 사용량을 분석 하 고 경고 하는 방법을 설명 합니다.