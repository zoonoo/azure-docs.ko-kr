---
title: 여러 지역에서 서비스 리소스를 이동 하는 방법
titleSuffix: Azure Cognitive Search
description: 이 문서에서는 azure 클라우드에서 azure Cognitive Search 리소스를 한 지역에서 다른 지역으로 이동 하는 방법을 보여 줍니다.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/18/2020
ms.openlocfilehash: 392c86d8ea24e59d388926d4df581305ea2b531d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599302"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Azure Cognitive Search 서비스를 다른 Azure 지역으로 이동

Azure 인식 서비스 계정을 한 지역에서 다른 지역으로 이동 하려면 구독을 이동 하는 내보내기 템플릿을 만듭니다. 구독을 이동한 후에는 데이터를 이동 하 고 서비스를 다시 만들어야 합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 템플릿을 내보냅니다.
> * 템플릿 수정: 대상 지역, 검색 및 저장소 계정 이름을 추가 합니다.
> * 템플릿을 배포 하 여 새 검색 및 저장소 계정을 만듭니다.
> * 새 지역에서 서비스 상태 확인
> * 원본 지역에서 리소스를 정리 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 계정에서 사용 하는 서비스 및 기능이 대상 지역에서 지원 되는지 확인 합니다.

- 미리 보기 기능에 대해 구독을 대상 지역에 대 한 허용 목록 확인 합니다. 미리 보기 기능에 대 한 자세한 내용은 [기술 자료 저장소](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [증분 보강](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)및 [개인 끝점](https://docs.microsoft.com/azure/search/service-create-private-endpoint)을 참조 하세요.

## <a name="assessment-and-planning"></a>평가 및 계획

검색 서비스를 새 지역으로 이동 하는 경우 [데이터를 새 저장소 서비스로 이동한](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) 다음 인덱스, 기술력과 및 기술 자료 저장소를 다시 작성 해야 합니다. 서비스를 보다 쉽고 빠르게 다시 빌드하기 위해 현재 설정을 기록 하 고 json 파일을 복사 해야 합니다.

## <a name="moving-your-search-services-resources"></a>검색 서비스의 리소스 이동

시작 하려면 리소스 관리자 템플릿을 내보낸 다음 수정 합니다.

### <a name="export-a-template"></a>템플릿 내보내기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 리소스 그룹 페이지로 이동 합니다.

> [!div class="mx-imgBorder"]
> ![리소스 그룹 페이지 예제](./media/search-move-resource/export-template-sample.png)

3. **모든 리소스**를 선택합니다.

3. 왼쪽 탐색 메뉴에서 **템플릿 내보내기**를 선택 합니다.

4. **템플릿 내보내기** 페이지에서 **다운로드** 를 선택 합니다.

5. 포털에서 다운로드 한 .zip 파일을 찾아 원하는 폴더에 해당 파일의 압축을 풉니다.

Zip 파일에는 템플릿을 배포 하는 템플릿 및 스크립트를 구성 하는. i n i 파일이 포함 되어 있습니다.

### <a name="modify-the-template"></a>템플릿 수정

검색 및 저장소 계정 이름 및 지역을 변경 하 여 템플릿을 수정 합니다. 이름은 각 서비스 및 지역 명명 규칙의 규칙을 따라야 합니다. 

지역 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조 하세요.  영역에 대 한 코드는 공백이 없고 **미국 중부** = **centralus**지역 이름입니다.

1. Azure Portal에서 **리소스 만들기**를 선택합니다.

2. **Marketplace 검색**에서 **템플릿 배포**를 입력하고 **ENTER**를 누릅니다.

3. **템플릿 배포**를 선택합니다.

4. **만들기**를 선택합니다.

5. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.

6. **파일 로드**를 선택 하 고 지침에 따라 이전 섹션에서 다운로드 하 여 압축을 푼 **템플릿. json** 파일을 로드 합니다.

7. **템플릿 json** 파일에서 검색 및 저장소 계정 이름의 기본값을 설정 하 여 대상 검색 및 저장소 계정의 이름을로 설정 합니다. 

8. 검색 및 저장소 서비스에 대 한 **템플릿. json** 파일의 **location** 속성을 대상 지역으로 편집 합니다. 이 예에서는 대상 지역을 `centralus`설정 합니다.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2015-08-19",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### <a name="deploy-the-template"></a>템플릿 배포

1. **템플릿. json** 파일을 저장 합니다.

2. 속성 값을 입력 하거나 선택 합니다.

- **구독**: Azure 구독을 선택합니다.

- **리소스 그룹**: **새로 만들기**를 클릭하고 리소스 그룹에 이름을 지정합니다.

- **위치**: Azure 위치를 선택 합니다.

3. **위에 명시 된 사용 약관에 동의 함** 확인란을 클릭 한 다음 **구매 선택** 단추를 클릭 합니다.

## <a name="verifying-your-services-status-in-new-region"></a>새 지역에서 서비스의 상태를 확인 하는 중

이동을 확인 하려면 새 리소스 그룹을 열고 서비스가 새 지역으로 나열 됩니다.

원본 지역에서 대상 지역으로 데이터를 이동 하려면 [새 저장소 계정으로 데이터를 이동 하는](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account)방법에 대 한이 문서의 지침을 참조 하세요.

## <a name="clean-up-resources-in-your-original-region"></a>원본 지역에서 리소스 정리

변경 내용을 커밋하고 서비스 계정의 이동을 완료 하려면 원본 서비스 계정을 삭제 합니다.

## <a name="next-steps"></a>다음 단계

[인덱스 만들기](https://docs.microsoft.com/azure/search/search-get-started-portal)

[기술 세트 만들기](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[기술 자료 저장소 만들기](https://docs.microsoft.com/azure/search/knowledge-store-create-portal)

