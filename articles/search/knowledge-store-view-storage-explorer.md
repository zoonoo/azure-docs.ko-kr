---
title: Storage Explorer를 사용하여 지식 저장소 보기
titleSuffix: Azure Cognitive Search
description: Azure Portal의 Storage Explorer를 사용하여 Azure Cognitive Search 지식 저장소를 살펴보고 분석합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: ba0b02067c032f9038051c169866588ded44af73
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85566018"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Storage Explorer를 사용하여 지식 저장소 보기

이 문서에서는 Azure Portal에서 Storage 탐색기를 사용 하 여 기술 자료 저장소에 연결 하 고 탐색 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

+ [Azure Portal에서 지식 저장소 만들기](knowledge-store-create-portal.md)의 단계에 따라 이 연습에 사용되는 샘플 지식 저장소를 만듭니다.

+ 지식 저장소를 만들 때 사용한 Azure 스토리지 계정의 이름과 Azure Portal의 계정 액세스 키도 필요합니다.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Storage 탐색기에서 기술 자료 저장소 보기, 편집 및 쿼리

1. Azure Portal에서 기술 자료 저장소를 만드는 데 사용한 [저장소 계정을 엽니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) .

1. 스토리지 계정의 왼쪽 탐색 창에서 **Storage Explorer**를 클릭합니다.

1. **테이블** 목록을 확장하여 호텔 리뷰 샘플 데이터에서 **데이터 가져오기** 마법사를 실행할 때 생성된 Azure 테이블 프로젝션 목록을 표시합니다.

핵심 구와 감정 점수를 포함 하 여 보강 데이터를 보려면 테이블을 선택 합니다.

   ![Storage Explorer에서 테이블 보기](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Storage Explorer에서 테이블 보기")

테이블 값에 대한 데이터 형식을 변경하거나 테이블의 개별 값을 변경하려면 **편집**을 클릭합니다. 한 테이블 행의 열에 대한 데이터 형식을 변경하면 모든 행에 적용됩니다.

   ![Storage Explorer에서 테이블 편집](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Storage Explorer에서 테이블 편집")

쿼리를 실행하려면 명령 모음에서 **쿼리**를 클릭하고 조건을 입력합니다.  

   ![Storage Explorer의 쿼리 테이블](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Storage Explorer의 쿼리 테이블")

## <a name="clean-up"></a>정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 분석을 위해이 기술 자료 저장소를 Power BI에 연결 하거나 REST API 및 Postman을 사용 하 여 다른 기술 자료 저장소를 만들어 코드로 이동 합니다.

> [!div class="nextstepaction"]
> [Power BI 연결](knowledge-store-connect-power-bi.md) 
>  [REST에서 기술 자료 저장소 만들기](knowledge-store-create-rest.md)
