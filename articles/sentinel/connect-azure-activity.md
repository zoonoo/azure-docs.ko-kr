---
title: Azure 활동 데이터를 Azure 센티널에 연결 | Microsoft Docs
description: Azure 활동 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 807a5ede3feee115b1a8dc51fe14966731fc7784
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240785"
---
# <a name="connect-data-from-azure-activity-log"></a>Azure 활동 로그에서 데이터 연결



한 번의 클릭으로 [Azure 활동 로그](../azure-monitor/platform/activity-logs-overview.md) 의 로그를 azure 센티널로 스트리밍할 수 있습니다. 활동 로그는 Azure에서 발생 한 구독 수준 이벤트에 대 한 통찰력을 제공 하는 구독 로그입니다. 여기에는 Azure Resource Manager 작동 데이터에서 서비스 상태 이벤트 업데이트에 이르기까지 광범위한 데이터가 포함됩니다. 활동 로그를 사용 하 여 구독의 리소스에 대해 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대 한 ' 무엇을, 누가, 언제 '를 결정할 수 있습니다. 또한 작업 및 기타 관련 속성의 상태도 이해할 수 있습니다. 활동 로그에는 읽기 (GET) 작업 또는 클래식/"RDFE" 모델을 사용 하는 리소스에 대 한 작업이 포함 되지 않습니다. 


## <a name="prerequisites"></a>사전 요구 사항

- 전역 관리자 또는 보안 관리자 권한이 있는 사용자


## <a name="connect-to-azure-activity-log"></a>Azure 활동 로그에 연결

1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음 **azure 활동 로그** 타일을 클릭 합니다.

2. Azure 활동 로그 창에서 Azure 센티널로 스트리밍할 구독을 선택 합니다. 

3. **연결**을 클릭합니다.

4. Azure 활동 경고에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **Azureactivity**를 검색 합니다.


 

## <a name="next-steps"></a>다음 단계
이 문서에서는 azure 활동 로그를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 얻는](quickstart-get-visibility.md)방법에 대해 알아봅니다.
- [Azure 센티널로 위협 검색을](tutorial-detect-threats-built-in.md)시작 합니다.
