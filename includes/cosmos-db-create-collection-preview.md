---
title: 포함 파일
description: 포함 파일
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/19/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: 331886f01345aba576cd8f96f95077f9bbdae704
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754343"
---
이제 Azure Portal에서 Data Explorer 도구를 사용하여 데이터베이스 및 컨테이너를 만들 수 있습니다. 

1. **Data Explorer** > **새 컨테이너**를 클릭합니다. 
    
    맨 오른쪽에 **컨테이너 추가** 영역이 표시되는데, 안 보이면 오른쪽으로 스크롤해야 합니다.

    ![Azure Portal Data Explorer, 컨테이너 추가 블레이드](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. **컨테이너 추가** 페이지에서 새 컨테이너의 설정을 입력합니다.

    |설정|제안 값|설명
    |---|---|---|
    |**데이터베이스 ID**|작업|새 데이터베이스의 이름으로 *작업*을 입력합니다. 데이터베이스 이름은 1-255자여야 하며, /, \\, #,? 또는 후행 공백은 포함할 수 없습니다. **데이터베이스 처리량 프로비전** 옵션을 선택합니다. 그러면 데이터베이스에 프로비저닝된 처리량을 데이터베이스 내 모든 컨테이너가 공유할 수 있습니다. 이 옵션은 비용 절감에도 도움이 됩니다. |
    |**처리량**|400|처리량을 400 RU/s(초당 요청 단위)로 유지합니다. 대기 시간을 줄이면 나중에 처리량을 늘릴 수 있습니다.| 
    |**컨테이너 ID**|Items|새 컨테이너의 이름으로 *Items*를 입력합니다. 컨테이너 ID에는 데이터베이스 이름과 동일한 문자 요구 사항이 적용됩니다.|
    |**파티션 키**| /category| 이 문서에 설명된 샘플은 파티션 키로 */category*를 사용합니다. 파티션 키를 설정하면 Azure Cosmos DB에서 애플리케이션의 스토리지 및 처리량 요구를 충족하도록 컬렉션을 확장할 수 있습니다. 일반적으로 파티션 키에 적합한 선택은 다양한 범위의 고유 값을 갖는 것이며, 그 결과 워크로드에서 스토리지 및 요청 볼륨의 균등한 분포를 가져옵니다. [분할에 대해 자세히 알아봅니다.](../articles/cosmos-db/partitioning-overview.md)|
    
    앞의 설정 외에도, 필요에 따라 컨테이너의 **고유 키**를 추가할 수 있습니다. 이 예에서 필드는 비워 두겠습니다. 고유 키는 데이터베이스에 데이터 무결성 계층을 추가할 수 있는 기능을 개발자에게 제공합니다. 컨테이너를 만드는 동안 고유 키 정책을 만들면 파티션 키당 하나 이상의 값에 대한 고유성이 보장됩니다. 자세한 내용은 [Azure Cosmos DB의 고유 키](../articles/cosmos-db/unique-keys.md) 문서를 참조하세요.
    
    **확인**을 선택합니다. Data Explorer에 새 데이터베이스와 컨테이너가 표시됩니다.

