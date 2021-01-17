---
title: MongoDB 작업에 대 한 Azure Cosmos DB API에 대 한 전송률 제한 오류를 방지 합니다.
description: SSR (서버 쪽 다시 시도) 기능을 사용 하 여 MongoDB 작업에 대 한 Azure Cosmos DB API가 rate 제한 오류 발생을 방지 하는 방법에 대해 알아봅니다.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 73c2aba3028f42621f241bd8f295e83e0ef96e68
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540608"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>MongoDB 작업에 대 한 Azure Cosmos DB API에 대 한 전송률 제한 오류 방지
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

MongoDB 작업에 대 한 Azure Cosmos DB API는 컬렉션의 처리량 제한 (RUs)을 초과 하는 경우 속도 제한 (16500/429) 오류가 발생 하 여 실패할 수 있습니다. 

서버 쪽 다시 시도 (SSR) 기능을 사용 하도록 설정 하 고 서버에서 이러한 작업을 자동으로 다시 시도 하도록 할 수 있습니다. 계정의 모든 컬렉션에 대해 짧은 지연 후 요청이 다시 시도 됩니다. 이 기능은 클라이언트 응용 프로그램에서 요율 제한 오류를 처리 하는 편리한 대안입니다.


## <a name="use-the-azure-portal"></a>Azure Portal 사용

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. MongoDB API에 대 한 Azure Cosmos DB API로 이동 합니다.

1. **설정** 섹션 아래에 있는 **기능** 창으로 이동 합니다.

1. **서버 쪽 다시 시도** 를 선택 합니다.

1. 계정에서 모든 컬렉션에 대해이 기능을 사용 하도록 설정 하려면 **사용** 을 클릭 합니다.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="MongoDB 용 Azure Cosmos DB API에 대 한 서버 쪽 다시 시도 기능의 스크린샷":::


## <a name="next-steps"></a>다음 단계

일반적인 오류 문제 해결에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* [MongoDB에 대 한 Azure Cosmos DB API의 일반적인 문제 해결](mongodb-troubleshoot.md)
