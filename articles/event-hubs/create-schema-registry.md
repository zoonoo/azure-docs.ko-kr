---
title: Azure Event Hubs 스키마 레지스트리 만들기
description: 이 문서에서는 Azure Event Hubs 네임 스페이스에서 스키마 레지스트리를 만드는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 90556e0843cda94dc79330321d027f8d28eb7d20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91652196"
---
# <a name="create-an-azure-event-hubs-schema-registry-preview"></a>Azure Event Hubs 스키마 레지스트리 만들기 (미리 보기)
이 문서에서는 Azure Event Hubs에서 호스트 하는 스키마 레지스트리에서 스키마를 사용 하 여 스키마 그룹을 만드는 방법을 보여 줍니다. Azure Event Hubs의 스키마 레지스트리 기능에 대 한 개요는 [Event Hubs의 Azure 스키마 레지스트리](schema-registry-overview.md)를 참조 하세요.

> [!NOTE]
> - **스키마 레지스트리** 기능은 현재 **미리 보기**에 있으므로 프로덕션 워크로드에는 추천되지 않습니다.
> - 이 기능은 **기본** 계층이 아닌 **표준** 및 **전용** 계층에서만 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
[Event Hubs 네임 스페이스를 만듭니다](event-hubs-create.md#create-an-event-hubs-namespace). 기존 네임 스페이스를 사용할 수도 있습니다. 

## <a name="create-a-schema-group"></a>스키마 그룹 만들기
1. **Event Hubs 네임 스페이스** 페이지로 이동 합니다. 
1. 왼쪽 메뉴에서 **스키마 레지스트리** 를 선택 합니다. 스키마 그룹을 만들려면 도구 모음에서 **+ 스키마 그룹** 을 선택 합니다. 

    :::image type="content" source="./media/create-schema-registry/namespace-page.png" alt-text="스키마 레지스트리 페이지":::
1. **스키마 그룹 만들기** 페이지에서 다음 단계를 수행 합니다.
    1. 스키마 그룹의 **이름을** 입력 합니다.
    1. **Serialization 형식**에서 스키마 그룹의 모든 스키마에 적용 되는 serialization 형식을 선택 합니다. 현재는 **avro** 형식만 지원 되므로 **avro**를 선택 합니다. 
    1. 그룹의 모든 스키마에 대 한 **호환성 모드** 를 선택 합니다. **Avro**의 경우 앞으로 및 이전 버전과의 호환성 모드가 지원 됩니다. 
    1. 그런 다음 **만들기** 를 선택 하 여 스키마 그룹을 만듭니다. 
1. 스키마 그룹 목록에서 **스키마 그룹** 의 이름을 선택 합니다.

    :::image type="content" source="./media/create-schema-registry/select-schema-group.png" alt-text="스키마 레지스트리 페이지":::    
1. 그룹에 대 한 **스키마 그룹** 페이지가 표시 됩니다.

    :::image type="content" source="./media/create-schema-registry/schema-group-page.png" alt-text="스키마 레지스트리 페이지":::
    

## <a name="add-a-schema-to-the-schema-group"></a>스키마 그룹에 스키마 추가
이 섹션에서는 Azure Portal를 사용 하 여 스키마 그룹에 스키마를 추가 합니다. 

1. **스키마 그룹** 페이지의 도구 모음에서 **+ 스키마** 를 선택 합니다. 
1. **스키마 만들기** 페이지에서 다음 단계를 수행 합니다.
    1. 스키마의 **이름을** 입력 합니다.
    1. 텍스트 상자에 다음 **스키마** 를 입력 합니다. 스키마를 사용 하 여 파일을 선택할 수도 있습니다.
    
        ```json
        {
            "type": "record",
            "name": "AvroUser",
            "namespace": "com.azure.schemaregistry.samples",
            "fields": [
                {
                    "name": "name",
                    "type": "string"
                },
                {
                    "name": "favoriteNumber",
                    "type": "int"
                }
            ]
        }
        ```
    1. **만들기**를 선택합니다. 
1. 스키마 목록에서 **스키마** 를 선택 합니다. 

    :::image type="content" source="./media/create-schema-registry/select-schema.png" alt-text="스키마 레지스트리 페이지":::
1. 스키마에 대 한 **스키마 개요** 페이지가 표시 됩니다. 

    :::image type="content" source="./media/create-schema-registry/schema-overview-page.png" alt-text="스키마 레지스트리 페이지":::    
1. 여러 버전의 스키마가 있는 경우 **버전** 드롭다운 목록에 표시 됩니다. 버전을 선택 하 여 해당 버전 스키마로 전환 합니다. 

## <a name="create-a-new-version-of-schema"></a>새 버전의 스키마 만들기

1. 텍스트 상자에서 스키마를 업데이트 하 고 **유효성 검사**를 선택 합니다. 다음 예에서는 새 필드가 `id` 스키마에 추가 되었습니다. 

    :::image type="content" source="./media/create-schema-registry/update-schema.png" alt-text="스키마 레지스트리 페이지":::    
    
1. 유효성 검사 상태 및 변경 내용을 검토 하 고 **저장**을 선택 합니다. 

    :::image type="content" source="./media/create-schema-registry/compare-save-schema.png" alt-text="스키마 레지스트리 페이지":::     
1. `2` **스키마 개요** 페이지에서 **버전** 에 대해가 선택 된 것을 볼 수 있습니다. 

    :::image type="content" source="./media/create-schema-registry/new-version.png" alt-text="스키마 레지스트리 페이지":::    
1. `1`스키마의 버전 1을 표시 하려면 선택 합니다. 

    :::image type="content" source="./media/create-schema-registry/select-version.png" alt-text="스키마 레지스트리 페이지":::    


## <a name="next-steps"></a>다음 단계
스키마 레지스트리에 대 한 자세한 내용은 [Event Hubs의 Azure 스키마 레지스트리](schema-registry-overview.md)를 참조 하세요.

