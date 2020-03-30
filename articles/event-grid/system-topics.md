---
title: Azure 이벤트 그리드의 시스템 항목
description: Azure 이벤트 그리드의 시스템 항목에 대해 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 35025bf1592f1293b9326d643f76322b4af590c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501199"
---
# <a name="system-topics-in-azure-event-grid"></a>Azure 이벤트 그리드의 시스템 항목
Azure 이벤트 그리드 서비스는 Azure 이벤트 원본에 대한 첫 번째 이벤트 구독을 만들 때 시스템 항목을 만듭니다. 현재 Event Grid는 2020년 3월 15일 이전에 만들어진 토픽 소스에 대한 시스템 토픽을 만들지 않습니다. 이 날짜 이후에 만든 모든 토픽 소스에 대해 Event Grid는 자동으로 시스템 토픽을 만듭니다. 이 문서에서는 Azure 이벤트 그리드의 **시스템 항목에** 대해 설명합니다.

> [!NOTE]
> 이 기능은 현재 Azure 정부 클라우드에서 사용할 수 없습니다. 

## <a name="overview"></a>개요
Azure Storage 계정과 같은 Azure 이벤트 원본에 대한 첫 번째 이벤트 구독을 만들 때 구독에 대한 프로비전 프로세스는 **Microsoft.EventGrid/systemTopics**형식의 추가 리소스를 만듭니다. Azure 이벤트 원본에 대한 마지막 이벤트 구독이 삭제되면 시스템 토픽이 자동으로 삭제됩니다.

시스템 토픽은 사용자 지정 토픽 시나리오, 즉 이벤트 그리드 토픽 및 이벤트 그리드 도메인에 적용되지 않습니다. 

## <a name="location"></a>위치
특정 지역/위치에 있는 Azure 이벤트 원본의 경우 시스템 항목은 Azure 이벤트 원본과 동일한 위치에 만들어집니다. 예를 들어 미국 동부의 Azure Blob 저장소에 대한 이벤트 구독을 만드는 경우 시스템 항목은 미국 동부에서 만들어집니다. Azure 구독, 리소스 그룹 또는 Azure Maps와 같은 전역 Azure 이벤트 소스의 경우 Event Grid는 **전역** 위치에서 시스템 항목을 만듭니다. 

## <a name="resource-group"></a>Resource group 
일반적으로 시스템 항목은 Azure 이벤트 소스가 있는 것과 동일한 리소스 그룹에서 만들어집니다. Azure 구독 범위에서 생성된 이벤트 구독의 경우 시스템 항목은 리소스 그룹 **Default-EventGrid**에서 만들어집니다. 리소스 그룹이 없는 경우 Azure Event Grid는 시스템 항목을 만들기 전에 리소스 그룹을 만듭니다. 

저장소 계정으로 리소스 그룹을 삭제하려고 하면 영향을 받는 리소스 목록에 시스템 항목이 표시됩니다.  

![리소스 그룹 삭제](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 

- [사용자 지정 항목](event-sources.md#custom-topics)
- [도메인](event-domains.md)