---
title: Azure Event Grid 이벤트에 대한 이벤트 처리기로 릴레이 하이브리드 연결
description: Azure Event Grid 이벤트에 대한 이벤트 처리기로 Azure Relay 하이브리드 연결을 사용하는 방법을 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0631724e688a71d7e9685f5f0ad738d81e2a8034
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596112"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Azure Event Grid 이벤트에 대한 이벤트 처리기로 릴레이 하이브리드 연결
이벤트 처리기는 이벤트가 전송된 위치입니다. 처리기는 이벤트를 처리하기 위한 추가 작업을 수행합니다. 이벤트를 처리하도록 여러 Azure 서비스가 자동으로 구성되며, **Azure Relay**가 그 중 하나입니다. 

Azure **Relay 하이브리드 연결**을 사용하여 엔터프라이즈 네트워크 내에 있으며 공개적으로 액세스할 수 있는 엔드포인트가 없는 애플리케이션에 이벤트를 보냅니다.

## <a name="tutorials"></a>자습서
Azure Relay 하이브리드 연결을 이벤트 처리기로 사용하는 예제는 다음 자습서를 참조하세요. 

|제목  |Description  |
|---------|---------|
| [자습서: 하이브리드 연결로 이벤트 전송](custom-event-to-hybrid-connection.md) | 수신기 애플리케이션에서 프로세싱에 대한 기존 하이브리드 연결에 사용자 지정 이벤트를 보냅니다. |

## <a name="next-steps"></a>다음 단계
지원되는 이벤트 처리기 목록은 [이벤트 처리기](event-handlers.md) 문서를 참조하세요. 