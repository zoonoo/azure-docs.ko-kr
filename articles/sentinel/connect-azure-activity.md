---
title: Azure 활동 데이터를 Azure 센티넬에 연결 | 마이크로 소프트 문서
description: Azure 활동 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
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
ms.openlocfilehash: 82dfcaf3394703aae531c828a1b96ad290bab798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124993"
---
# <a name="connect-data-from-azure-activity-log"></a>Azure 활동 로그에서 데이터 연결

한 번의 클릭으로 [Azure 활동 로그인에서](../azure-monitor/platform/platform-logs-overview.md) Azure Sentinel로 로그를 스트리밍할 수 있습니다. 활동 로그는 Azure 리소스 관리자 운영 데이터에서 서비스 상태 이벤트에 대한 업데이트에 이르기까지 Azure 전체의 구독 수준 이벤트를 기록하고 표시하는 구독 로그입니다. 활동 로그를 사용하여 구독의 리소스에서 수행된 쓰기 작업(PUT, POST, DELETE)에 대해 '무엇을, 누가, 언제'를 결정할 수 있습니다. 작업 및 기타 관련 속성의 상태를 학습할 수도 있습니다. 활동 로그에는 클래식/"RDFE" 모델을 사용하는 리소스에 대한 읽기(GET) 작업 또는 작업이 포함되지 않습니다. 

## <a name="prerequisites"></a>사전 요구 사항

- 사용자는 Log Analytics 작업 영역에 대한 기여자 권한이 있어야 합니다.
- 사용자는 Azure Sentinel로 스트리밍하려는 로그를 구독에 대한 Reader 권한이 있어야 합니다.

## <a name="set-up-the-azure-activity-connector"></a>Azure 활동 커넥터 설정

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터를 선택합니다.** 커넥터 목록에서 **Azure 활동을**클릭한 다음 오른쪽 하단의 **연결선 열기 페이지** 단추를 클릭합니다.

2. **지침** 탭에서 Azure 활동 >링크 **구성을 클릭합니다.**

3. Azure **활동 로그** 창에서 Azure Sentinel로 스트리밍할 로그가 있는 구독을 선택합니다. 

4. 오른쪽으로 열리는 구독 창에서 **연결을**클릭합니다.

5. Azure 활동 경고에 대한 로그 분석에서 관련 `AzureActivity` 스키마를 사용하려면 쿼리 창을 입력합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 활동 로그를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [기본 제공](tutorial-detect-threats-built-in.md) 또는 [사용자 지정](tutorial-detect-threats-custom.md) 규칙을 사용하여 Azure Sentinel을 사용하여 위협 탐지를 시작하세요.
