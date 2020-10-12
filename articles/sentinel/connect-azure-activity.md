---
title: Azure 활동 데이터를 Azure 센티널에 연결 | Microsoft Docs
description: 한 번의 클릭으로 azure 활동 로그를 Azure 센티널로 스트리밍합니다. 활동 로그는 Azure에서 구독 수준 이벤트를 기록 하 고 표시 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 88f363406ec40dfa439a52ad351501d1c64dce95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564490"
---
# <a name="connect-data-from-azure-activity-log"></a>Azure 활동 로그에서 데이터 연결

한 번의 클릭으로 [Azure 활동 로그](../azure-monitor/platform/platform-logs-overview.md) 의 로그를 azure 센티널로 스트리밍할 수 있습니다. 활동 로그는 Azure Resource Manager 작동 데이터에서 Service Health 이벤트의 업데이트에 이르기까지 Azure에서 구독 수준 이벤트를 기록 하 고 표시 하는 구독 로그입니다. 활동 로그를 사용 하 여 구독에서 리소스에 대해 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대 한 ' 무엇을, 누가, 언제 '를 결정할 수 있습니다. 작업 및 기타 관련 속성의 상태를 배울 수도 있습니다. 활동 로그에는 읽기 (GET) 작업 또는 클래식/"RDFE" 모델을 사용 하는 리소스에 대 한 작업이 포함 되지 않습니다. 

## <a name="prerequisites"></a>사전 요구 사항

- 사용자에 게 Log Analytics 작업 영역에 대 한 참가자 권한이 있어야 합니다.
- 사용자에 게 Azure 센티널로 스트리밍할 로그가 포함 된 모든 구독에 대 한 읽기 권한이 있어야 합니다.

## <a name="set-up-the-azure-activity-connector"></a>Azure 작업 커넥터 설정

1. Azure 센티널 탐색 메뉴에서 **데이터 커넥터**를 선택 합니다. 커넥터 목록에서 **Azure 활동**을 클릭 한 다음 오른쪽 아래에 있는 **커넥터 페이지 열기** 단추를 클릭 합니다.

2. **지침** 탭에서 **Azure 활동 로그 구성 >** 링크를 클릭 합니다.

3. **Azure 활동 로그** 창에서 azure 센티널로 스트리밍할 로그가 포함 된 구독을 선택 합니다. 

4. 오른쪽에 열리는 구독 창에서 **연결**을 클릭 합니다.

5. Azure 활동 경고에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 `AzureActivity` 쿼리 창에를 입력 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 azure 활동 로그를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [기본 제공](tutorial-detect-threats-built-in.md) 또는 [사용자 지정](tutorial-detect-threats-custom.md) 규칙을 사용 하 여 Azure 센티널에서 위협을 검색 하기 시작 합니다.
