---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90068741"
---
**SDK 사용 중지에 대한 알림을 어떻게 받나요?**

Microsoft는 지원되는 SDK로 매끄럽게 전환할 수 있도록 사용 중지되는 SDK 지원이 종료되기 12개월 전에 사전 알림을 제공합니다. Azure Portal, Azure 업데이트, 할당된 서비스 관리자에 대한 직접 통신 등 다양한 통신 채널을 통해 알려드립니다.

**12개월 동안 사용 중지 예정된 Azure Cosmos DB SDK를 사용하여 애플리케이션을 제작할 수 있나요?** 

예, 12개월 알림 기간 동안 사용 중지 예정된 Azure Cosmos DB SDK를 사용하여 애플리케이션을 제작, 배포 및 수정할 수 있습니다. 12개월 알림 기간 동안 지원되는 최신 버전의 Azure Cosmos DB SDK로 적절하게 마이그레이션하는 것이 좋습니다. 

**사용 중지 날짜 후에는 지원되지 않는 Azure Cosmos DB SDK를 사용하는 애플리케이션은 어떻게 되나요?** 

사용 중지 날짜가 지나면 Azure Cosmos DB는 더 이상 버그를 수정하거나 새 기능을 추가하거나 사용 중지된 SDK 버전을 지원하지 않습니다. 업그레이드하지 않으려는 경우 SDK의 사용 중지 버전에서 보낸 요청은 Azure Cosmos DB 서비스에서 계속 제공됩니다. 

**최신 기능과 업데이트를 포함하는 SDK 버전은 무엇인가요?**

새 기능 및 업데이트는 지원되는 최신 주요 SDK 버전의 최신 부 버전에만 추가됩니다. 항상 최신 버전을 사용하여 새로운 기능, 성능 향상 및 버그 수정을 활용하는 것이 좋습니다. 사용 중지되지 않은 오래된 버전의 SDK를 사용하는 경우 Azure Cosmos DB 요청이 작동하지만 새로운 기능에 액세스할 수는 없습니다.  

**마감 날짜 전에 내 애플리케이션을 업데이트할 수 없는 경우 어떻게 해야 하나요?**

가능한 한 빨리 최신 SDK로 업그레이드하는 것이 좋습니다. SDK가 사용 중지로 태깅된 후 12개월 내에 애플리케이션을 업데이트해야 합니다. 사용 중지 날짜까지 업데이트할 수 없는 경우 SDK 사용 중지 버전에서 전송된 요청은 Azure Cosmos DB에서 계속 제공되므로 실행 중인 애플리케이션은 계속 작동합니다. 그러나 Azure Cosmos DB는 더 이상 버그를 수정하거나 새 기능을 추가하거나 사용 중지된 SDK 버전을 지원하지 않습니다. 

지원 계획이 있고 기술 지원이 필요한 경우 지원 티켓을 제출하여 [문의해주세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
    


