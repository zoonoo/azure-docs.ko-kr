---
title: Storage Explorer를 사용하여 지식 저장소 보기 - Azure Search
description: Azure Portal의 Storage Explorer를 사용하여 Azure Search 지식 저장소를 살펴보고 분석합니다.
author: lisaleib
services: search
ms.service: search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: 9ec93bcd2309bb47b24983260c35726ac24b5e95
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265609"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Storage Explorer를 사용하여 지식 저장소 보기

> [!Note]
> 지식 저장소는 미리 보기로 있으므로 프로덕션 환경에서 사용하면 안 됩니다. 이 기능은 [Azure Search REST API 버전 2019-05-06-Preview](search-api-preview.md)에서 제공됩니다. 지금은 .NET SDK 지원이 없습니다.
>
이 문서에서는 Azure Portal의 Storage Explorer를 사용하여 지식 저장소에 연결하고 검색하는 방법을 알아봅니다. 이 연습에 사용되는 지식 저장소 샘플을 만들려면 [Azure Portal에서 지식 저장소 만들기](knowledge-store-create-portal.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

+ [Azure Portal에서 지식 저장소 만들기](knowledge-store-create-portal.md)의 단계에 따라 이 연습에 사용되는 샘플 지식 저장소를 만듭니다.

+ 지식 저장소를 만들 때 사용한 Azure 스토리지 계정의 이름과 Azure Portal의 계정 액세스 키도 필요합니다.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Storage Explorer에서 지식 저장소 보기, 편집 및 쿼리

1. Azure Portal에서 지식 저장소를 만드는 데 사용한 스토리지 계정을 엽니다.

1. 스토리지 계정의 왼쪽 탐색 창에서 **Storage Explorer**를 클릭합니다.

1. **테이블** 목록을 확장하여 호텔 리뷰 샘플 데이터에서 **데이터 가져오기** 마법사를 실행할 때 생성된 Azure 테이블 프로젝션 목록을 표시합니다.

핵심 구 감정 점수, 위도 및 경도 위치 데이터 등을 비롯한 보강 데이터를 보려면 원하는 테이블을 선택합니다.

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

이 지식 저장소를 Power BI에 연결하는 방법을 알아보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Power BI와 연결](knowledge-store-connect-power-bi.md)

REST API 및 Postman을 사용하여 지식 저장소를 만드는 방법은 다음 문서를 참조하세요.  

> [!div class="nextstepaction"]
> [REST에서 지식 저장소 만들기](knowledge-store-howto.md)
