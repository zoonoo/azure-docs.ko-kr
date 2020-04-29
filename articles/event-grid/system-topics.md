---
title: Azure Event Grid의 시스템 항목
description: Azure Event Grid의 시스템 항목에 대해 설명 합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 46bceeb31fa38068c6c4f9f3a86ed556ad39effb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393158"
---
# <a name="system-topics-in-azure-event-grid"></a>Azure Event Grid의 시스템 항목
Azure Event Grid 서비스는 Azure 이벤트 원본에 대 한 첫 번째 이벤트 구독을 만들 때 시스템 항목을 만듭니다. 현재 Event Grid는 3 월 15 2020 일 전에 만들어진 토픽 원본에 대 한 시스템 항목을 만들지 않습니다. 이 날짜 이후에 또는 이후에 만든 모든 토픽 원본에 대해 Event Grid는 시스템 항목을 자동으로 만듭니다. 이 문서에서는 Azure Event Grid의 **시스템 항목** 을 설명 합니다.

> [!NOTE]
> 이 기능은 현재 Azure Government 클라우드에 대해 사용 하도록 설정 되어 있지 않습니다. 

## <a name="overview"></a>개요
Azure Storage 계정과 같은 Azure 이벤트 원본에 대 한 첫 번째 이벤트 구독을 만들 때 구독에 대 한 프로 비전 프로세스에서 **Microsoft EventGrid/systemTopics**형식의 추가 리소스를 만듭니다. Azure 이벤트 원본에 대 한 마지막 이벤트 구독을 삭제 하면 시스템 항목이 자동으로 삭제 됩니다.

시스템 항목은 사용자 지정 항목 시나리오, 즉 Event Grid 토픽 및 Event Grid 도메인에는 적용 되지 않습니다. 

## <a name="location"></a>위치
특정 지역/위치에 있는 Azure 이벤트 원본의 경우 system 토픽은 Azure 이벤트 원본과 동일한 위치에 만들어집니다. 예를 들어 미국 동부에서 Azure blob 저장소에 대 한 이벤트 구독을 만드는 경우 미국 동부에서 시스템 항목이 생성 됩니다. Azure 구독, 리소스 그룹, Azure Maps 등의 글로벌 Azure 이벤트 원본에 대 한 Event Grid는 **글로벌** 위치에 시스템 항목을 만듭니다. 

## <a name="resource-group"></a>Resource group 
일반적으로 시스템 항목은 Azure 이벤트 원본이 있는 것과 동일한 리소스 그룹에 만들어집니다. Azure 구독 범위에서 만든 이벤트 구독의 경우 system 토픽은 리소스 그룹 **기본-EventGrid**아래에 생성 됩니다. 리소스 그룹이 없는 경우 시스템 항목을 만들기 전에 Azure Event Grid 만듭니다. 

저장소 계정을 사용 하 여 리소스 그룹을 삭제 하려고 하면 영향을 받는 리소스 목록에 시스템 항목이 표시 됩니다.  

![리소스 그룹 삭제](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 

- [사용자 지정 항목](custom-topics.md)
- [도메인](event-domains.md)