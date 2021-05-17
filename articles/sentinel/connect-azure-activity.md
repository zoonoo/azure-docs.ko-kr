---
title: Azure 활동 데이터를 Azure Sentinel에 연결 | Microsoft Docs
description: 한 번의 클릭으로 Azure 활동 로그를 Azure Sentinel로 스트리밍합니다. 활동 로그는 Azure에서의 구독 수준 이벤트를 기록하고 표시합니다.
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
ms.openlocfilehash: 8f0bac363e020826e34df3cb0b4e3d3582990302
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595453"
---
# <a name="connect-data-from-azure-activity-log"></a>Azure 활동 로그에서 데이터 연결

한 번의 클릭으로 [Azure 활동 로그](../azure-monitor/essentials/platform-logs-overview.md)의 로그를 Azure Sentinel로 스트리밍할 수 있습니다. 활동 로그는 Azure Resource Manager 운영 데이터에서 Service Health 이벤트의 업데이트에 이르기까지 Azure에서의 구독 수준 이벤트를 기록하고 표시하는 구독 로그입니다. 활동 로그를 사용하면 구독의 리소스에서 수행된 쓰기 작업(PUT, POST, DELETE)의 '누가, 무엇을, 언제'를 확인할 수 있습니다. 또한 작업 및 기타 관련 속성의 상태도 알 수 있습니다. 읽기(GET) 작업 또는 Classic/’RDFE’ 모델을 사용하는 리소스에 대한 작업은 활동 로그에 포함되지 않습니다. 

## <a name="prerequisites"></a>사전 요구 사항

- 사용자에게 Log Analytics 작업 영역에 대한 기여자 권한이 있어야 합니다.
- 사용자에게 Azure Sentinel로 스트리밍할 로그가 포함된 모든 구독에 대한 읽기 권한이 있어야 합니다.

## <a name="set-up-the-azure-activity-connector"></a>Azure 활동 커넥터 설정

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다. 커넥터 목록에서 **Azure 활동** 을 클릭한 다음 오른쪽 아래에 있는 **커넥터 페이지 열기** 단추를 클릭합니다.

2. **지침** 탭에서 **Azure 활동 로그 구성 >** 링크를 클릭합니다.

3. **Azure 활동 로그** 창에서 Azure Sentinel로 스트리밍할 로그가 포함된 구독을 선택합니다. 

4. 오른쪽에 열리는 구독 창에서 **연결** 을 클릭합니다.

5. Azure 활동 경고의 Log Analytics에서 관련 스키마를 사용하려면 쿼리 창에 `AzureActivity`를 입력합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 활동 로그를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [기본 제공](tutorial-detect-threats-built-in.md) 또는 [사용자 지정](tutorial-detect-threats-custom.md) 규칙을 사용하여 Azure Sentinel에서 위협 검색 시작하기.
