---
title: Azure Automation 변경 내용 추적 및 인벤토리에 대한 경고를 만드는 방법
description: 이 문서에서는 변경 내용 추적 및 인벤토리에 의해 탐지된 변경의 상태에 대해 알리도록 Azure 경고를 구성하는 방법에 대해 설명합니다.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/15/2020
ms.topic: conceptual
ms.openlocfilehash: 91be2f8641a061d009962cdcd03a8d56048594da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594508"
---
# <a name="how-to-create-alerts-for-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리에 대한 경고를 만드는 방법

Azure의 경고는 Runbook 작업, 서비스 상태 문제 또는 Automation 계정과 관련된 기타 시나리오의 결과를 사전에 알려 줍니다. Azure Automation에는 미리 구성된 경고 규칙이 포함되어 있지 않지만 생성되는 데이터를 기반으로 사용자 고유의 경고 규칙을 만들 수 있습니다. 이 문서에서는 변경 내용 추적 및 인벤토리에 의해 식별되는 변경 내용에 따라 경고 규칙을 만드는 방법에 대한 지침을 제공합니다.

Azure Monitor 경고에 익숙하지 않은 경우 시작하기 전에 [Microsoft Azure의 경고 개요](../../azure-monitor/alerts/alerts-overview.md)를 참조하세요. 로그 쿼리를 사용하는 경고에 대한 자세한 내용은 [Azure Monitor의 로그 경고](../../azure-monitor/alerts/alerts-unified-log.md)를 참조하세요.

## <a name="create-alert"></a>경고 만들기

다음 예에서는 머신에서 **C:\windows\system32\drivers\etc\hosts** 파일이 수정되었음을 보여 줍니다. 이 파일은 Windows가 호스트 이름을 IP 주소로 확인하는 데 사용하기 때문에 중요합니다. 이 작업은 DNS보다 우선하므로 연결 문제가 발생할 수 있습니다. 악의적인 또는 위험한 웹 사이트로 트래픽 리디렉션이 발생할 수도 있습니다.

![호스트 파일 변경 내용을 보여 주는 차트](./media/configure-alerts/changes.png)

이 예제를 사용하여 변경에 대한 경고를 만드는 단계를 설명하겠습니다.

1. Automation 계정의 **변경 내용 추적** 페이지에서 **Log Analytics** 를 선택합니다.

2. 로그 검색에서 `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` 쿼리를 사용하여 **hosts** 파일의 콘텐츠 변경 내용을 찾습니다. 이 쿼리는 단어 `hosts`를 포함하는 정규화된 경로 이름을 갖는 파일에서의 콘텐츠 변경을 검색합니다. 경로 부분을 정규화된 형식으로 변경하여(예: `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`를 사용) 특정 파일을 요청할 수도 있습니다.

3. 쿼리에서 해당 결과가 반환되면 로그 검색에서 **새 경고 규칙** 을 선택하여 **경고 만들기** 페이지를 엽니다. Azure Portal에서 **Azure Monitor** 를 통해 이 페이지로 이동할 수도 있습니다.

4. 쿼리를 다시 확인하고 경고 논리를 수정합니다. 여기서는 환경의 모든 컴퓨터에서 변경 내용이 여러 개 검색되면 경고를 트리거할 수 있습니다.

    ![hosts 파일 변경 내용을 추적하기 위한 쿼리의 변경](./media/configure-alerts/change-query.png)

5. 경고 논리를 설정한 후에는 경고 트리거에 대응하여 작업을 수행할 작업 그룹을 할당합니다. 이 경우 이메일을 보내도록 설정하고 ITSM(IT 서비스 관리) 티켓을 만듭니다.

업데이트 배포 상태를 알려 주도록 경고를 설정하려면 아래 단계를 따르세요. Azure 경고를 처음 사용하는 경우 [Azure 경고 개요](../../azure-monitor/alerts/alerts-overview.md)를 참조하세요.

## <a name="configure-action-groups-for-your-alerts"></a>경고에 대한 작업 그룹 구성

경고가 구성되었으면 여러 경고에서 사용할 작업 그룹을 설정할 수 있습니다. 이 작업에는 이메일 알림, Runbook, webhook 등이 포함될 수 있습니다. 작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](../../azure-monitor/alerts/action-groups.md)를 참조하세요.

1. 경고를 선택한 다음, **작업 그룹** 에서 **새로 만들기** 를 선택합니다.

2. 작업 그룹의 전체 이름과 약식 이름을 입력합니다. 업데이트 관리는 지정된 그룹을 사용하여 알림을 보낼 때 약식 이름을 사용합니다.

3. **작업** 에서 작업을 지정하는 이름(예: **이메일 알림**)을 입력합니다.

4. **작업 유형** 에서 적절한 유형(예: **이메일/SMS 메시지/푸시/음성**)을 선택합니다.

5. 작업 세부 정보를 편집하려면 연필 아이콘을 선택합니다.

6. 작업 유형에 대한 창을 채웁니다. 예를 들어 **이메일/SMS 메시지/푸시/음성** 을 사용하여 이메일을 보내는 경우 작업 이름을 입력하고 **이메일** 확인란을 선택하고 유효한 이메일 주소를 입력한 다음 **확인** 을 선택합니다.

    ![이메일 작업 그룹 구성](./media/configure-alerts/configure-email-action-group.png)

7. 작업 그룹 추가 창에서 **확인** 을 선택합니다.

8. 경고 이메일의 경우 이메일 제목을 사용자 지정할 수 있습니다. **규칙 만들기** 에서 **작업 사용자 지정** 을 선택한 다음, **이메일 제목** 을 선택합니다.

9. 작업이 완료되면 **경고 규칙 만들기** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

* Azure Monitor의 [경고](../../azure-monitor/alerts/alerts-overview.md)에 대해 자세히 알아봅니다.

* Log Analytics 작업 영역에서 데이터를 검색하고 분석하는 [로그 쿼리](../../azure-monitor/logs/log-query-overview.md)에 대해 알아봅니다.

* [Azure Monitor 로그를 사용하여 사용량 및 비용](../../azure-monitor/logs/manage-cost-storage.md) 관리는 데이터 보존 기간을 변경하여 비용을 제어하는 방법 및 데이터 사용량에 대한 분석 및 경고 방법을 설명합니다.