---
title: Azure Event Grid에서 이벤트 도메인에 대 한 사용 사례
description: 이 문서에서는 Azure Event Grid에서 이벤트 도메인을 사용 하는 몇 가지 사용 사례를 설명 합니다.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 00318fc78053ed55e3599c329746d89d2eee4f99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204408"
---
# <a name="use-cases-for-event-domains-in-azure-event-grid"></a>Azure Event Grid에서 이벤트 도메인에 대 한 사용 사례
이 문서에서는 Azure Event Grid에서 이벤트 도메인을 사용 하는 몇 가지 사용 사례를 설명 합니다. 

## <a name="use-case-1"></a>사용 사례 1 
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="use-case-2"></a>사용 사례 2
시스템 토픽을 사용 하는 경우에는 이벤트 구독 수가 500 개로 제한 됩니다. 시스템 토픽에 대해 500 개 이상의 이벤트 구독을 필요로 하는 경우 도메인을 사용할 수 있습니다. 

Azure Blob Storage에 대 한 시스템 항목을 만들었으며 항목에 대해 500 개 이상의 구독을 만들어야 하지만 제한 (토픽 당 500 구독) 때문에 가능 하지 않다고 가정 합니다. 이 경우 이벤트 도메인을 사용 하는 다음 솔루션을 사용할 수 있습니다. 

1. 최대 10만 항목을 지원할 수 있는 도메인을 만들고 각 도메인 항목이 500 이벤트 구독을 가질 수 있습니다. 이 모델은 500 * 10만 이벤트 구독을 제공 합니다. 
1. Azure Storage 시스템 항목에 대 한 Azure 함수 구독을 만듭니다. 함수는 Azure storage에서 이벤트를 수신 하는 경우 적절 한 도메인 토픽에 이벤트를 보강 하 고 게시할 수 있습니다. 예를 들어 함수는 blob 파일 이름을 구문 분석 하 여 대상 도메인 항목을 확인 하 고 도메인에 이벤트를 게시할 수 있습니다. 

:::image type="content" source="./media/event-domains-use-cases/use-case-2.jpg" alt-text="AzureEvent Grid 도메인-사용 사례 2":::


## <a name="next-steps"></a>다음 단계
이벤트 도메인을 설정 하 고, 항목을 만들고, 이벤트 구독을 만들고, 이벤트를 게시 하는 방법에 대해 알아보려면 [이벤트 도메인 관리](./how-to-event-domains.md)를 참조 하세요.
