---
title: "Azure Cosmos DB 테이블 API를 사용하여 사용할 데이터 가져오기 | Microsoft Docs"
description: "Azure Cosmos DB Table API를 사용하여 데이터를 가져오는 방법을 알아봅니다."
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: b60743e2-0227-43ab-965a-0ae3ebacd917
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 984510b2dae99849507953163f94ad2f925651cd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="import-data-for-use-with-the-azure-cosmos-db-table-api"></a>Azure Cosmos DB Table API를 사용하여 데이터 가져오기

이 자습서에서는 Azure Cosmos DB [테이블 API](table-introduction.md)로 사용할 데이터 가져오기에 대한 지침을 제공합니다. Azure 테이블 저장소에 저장된 데이터가 있는 경우 데이터 마이그레이션 도구 또는 az 복사본을 사용하여 데이터를 Azure Cosmos DB로 가져올 수 있습니다. 데이터를 가져오면 Azure Cosmos DB가 제공하는 프리미엄 기능(턴키 방식의 글로벌 배포, 전용 처리량, 99번째 백분위수에서 1자리 밀리초 대기 시간, 보장된 고가용성 및 자동 보조 인덱싱)을 활용할 수 있습니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 마이그레이션 도구를 사용하여 데이터 가져오기
> * AzCopy를 사용하여 데이터 가져오기

## <a name="data-migration-tool"></a>데이터 마이그레이션 도구

Azure Cosmos DB 데이터 마이그레이션 도구는 기존 Azure 테이블 저장 데이터를 가져오기 위한 하나의 옵션입니다. 이 도구를 사용하여 데이터를 가져 오려면 Azure Table Storage를 원본으로, Azure Cosmos DB Table API를 대상으로 선택하고 나머지 정보는 도구에 요청된 대로 제공합니다. 데이터를 대량으로 내보내거나 순차 레코드 가져오기를 사용하여 내보낼 수 있습니다. 

Azure Table Storage를 원본으로, 테이블 API를 대상으로 정의하는 정보는 다음 섹션에서 제공됩니다.
- [Azure Table Storage를 데이터 마이그레이션 원본으로 사용](import-data.md#AzureTableSource). 
- [대량 가져오기로 테이블 API를 사용하여 내보내기](import-data.md#tableapibulkexport)
- [순차적 레코드 가져오기를 사용하여 테이블 API로 내보내기](import-data.md#tableapiseqtarget).

## <a name="azcopy-command"></a>AzCopy 명령

AzCopy 명령줄 유틸리티를 사용하는 것은 Azure Table Storage에서 Azure Cosmos DB 테이블 API로 데이터를 마이그레이션하는 다른 옵션입니다. AzCopy를 사용하려면 먼저 [테이블 저장소에서 데이터 내보내기](../storage/common/storage-use-azcopy.md#export-data-from-table-storage)에 설명된 대로 데이터를 매니페스트 파일로 내보낸 다음 매니페스트 파일의 데이터를 [Azure Cosmos DB 테이블 API](../storage/common/storage-use-azcopy.md#import-data-into-table-storage)로 가져옵니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 데이터 마이그레이션 도구를 사용하여 데이터 가져오기
> * AzCopy를 사용하여 데이터 가져오기

이제 다음 자습서로 진행하여 Azure Cosmos DB 테이블 API를 사용하여 데이터를 쿼리하는 방법을 알아볼 수 있습니다. 

> [!div class="nextstepaction"]
>[데이터는 어떻게 쿼리하나요?](../cosmos-db/tutorial-query-table.md)



