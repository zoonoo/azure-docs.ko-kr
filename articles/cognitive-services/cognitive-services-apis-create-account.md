---
title: Azure Portal에서 Cognitive Services 리소스 만들기
titleSuffix: Azure Cognitive Services
description: Azure Portal에서 리소스를 만들고 구독 하 여 Azure Cognitive Services를 시작 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: c7db2b4d49e3b9297c32d2e11ffe7c7702c17544
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274632"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Cognitive Services 리소스 만들기

이 빠른 시작을 사용 하 여 Azure Portal를 사용 하 여 Azure Cognitive Services 리소스를 만듭니다. Cognitive Services 리소스를 성공적으로 만든 후에는 응용 프로그램을 인증 하는 데 사용할 수 있는 끝점 및 키를 가져옵니다.

## <a name="prerequisites"></a>필수 구성 요소

* 유효한 Azure 구독- [무료로 하나를 만듭니다](https://azure.microsoft.com/free/).

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>새 Azure Cognitive Services 리소스 만들기

Cognitive Services 리소스를 만들기 전에 리소스를 포함 하는 Azure 리소스 그룹이 있어야 합니다. 새 리소스를 만들 때 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용 하는 옵션이 있습니다. 이 문서에서는 새 리소스 그룹을 만드는 방법을 보여 줍니다.

1. [Azure Portal](https://portal.azure.com)에 로그인한 후 **+리소스 만들기**를 클릭합니다.

    ![Cognitive Services API 선택](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. 앞서 설명한 것 처럼 다중 서비스 리소스 또는 단일 서비스 리소스를 사용 하 여 두 가지 방법으로 Cognitive Services 리소스를 만들 수 있습니다.

    #### <a name="multi-service-resourcetabmultiservice"></a>[다중 서비스 리소스](#tab/multiservice)

    다중 서비스 리소스를 만들려면 검색 표시줄에 **Cognitive Services** 을 입력 합니다.

    ![Cognitive Services 검색](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    Cognitive Services 페이지에서 **만들기**를 선택 합니다.

    ![Cognitive Services 계정 만들기](media/cognitive-services-apis-create-account/azurecogservsearchmulti-2.png)

    #### <a name="single-service-resourcetabsingleservice"></a>[단일 서비스 리소스](#tab/singleservice)

    사용 가능한 인식 서비스를 모두 보려면 **Azure Marketplace**에서 **AI + Machine Learning**를 선택 합니다. 관심이 있는 서비스가 표시 되지 않으면 **모두 보기** 를 클릭 하 고 **Cognitive Services**로 스크롤합니다. **자세히** 보기를 클릭 하면 Cognitive Services의 전체 카탈로그를 볼 수 있습니다.

    관심이 있는 서비스를 만들었으면 **만들기**를 클릭 합니다.
    
    ![Cognitive Services API 선택](media/cognitive-services-apis-create-account/azureMarketplace.png)

    ***
3. **만들기** 페이지에서 다음 정보를 제공합니다.

    #### <a name="multi-service-resourcetabmultiservice"></a>[다중 서비스 리소스](#tab/multiservice)

    |    |    |
    |--|--|
    | **이름** | Cognitive Services 리소스를 설명하는 이름입니다. 예를 들면 *MyCognitiveServicesResource*입니다. |
    | **구독** | 사용 가능한 Azure 구독 중 하나를 선택합니다. |
    | **위치** | Cognitive Service 인스턴스의 위치입니다. 다른 위치를 사용하면 대기 시간이 발생할 수 있지만 리소스의 런타임 가용성에는 영향을 주지 않습니다. Azure Cognitive Services를 호출할 때 필요할 수 있으므로 Azure 위치를 염두에 두어야 합니다. |
    | **가격 책정 계층** | Cognitive Services 계정의 비용은 선택한 옵션 및 사용량에 따라 다릅니다. 자세한 내용은 API [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/)를 참조하세요.
    | **리소스 그룹** | Cognitive Services 리소스를 포함 하는 Azure 리소스 그룹입니다. 새 그룹을 만들거나 기존 그룹에 추가할 수 있습니다. |

    ![리소스 만들기 화면](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    **만들기**를 클릭합니다.

    #### <a name="single-service-resourcetabsingleservice"></a>[단일 서비스 리소스](#tab/singleservice)

    |    |    |
    |--|--|
    | **이름** | Cognitive Services 리소스를 설명하는 이름입니다. 예를 들면 *TextAnalyticsResource*입니다. |
    | **구독** | 사용 가능한 Azure 구독 중 하나를 선택합니다. |
    | **위치** | Cognitive Service 인스턴스의 위치입니다. 다른 위치를 사용하면 대기 시간이 발생할 수 있지만 리소스의 런타임 가용성에는 영향을 주지 않습니다. Azure Cognitive Services를 호출할 때 필요할 수 있으므로 Azure 위치를 염두에 두어야 합니다. |
    | **가격 책정 계층** | Cognitive Services 계정의 비용은 선택한 옵션 및 사용량에 따라 다릅니다. 자세한 내용은 API [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/)를 참조하세요.
    | **리소스 그룹** | Cognitive Services 리소스를 포함 하는 Azure 리소스 그룹입니다. 새 그룹을 만들거나 기존 그룹에 추가할 수 있습니다. |

    ![리소스 만들기 화면](media/cognitive-services-apis-create-account/resource_create_screen.png)

    **만들기**를 클릭합니다.

    ***

## <a name="get-the-keys-for-your-resource"></a>리소스의 키를 가져옵니다.

리소스가 성공적으로 생성 되 면 화면 오른쪽 위에서 팝업 알림을 받게 됩니다. 알림에서 **리소스로 이동** 을 클릭 하 여 만든 인식 서비스 리소스를 확인 합니다. 

![인식 서비스 리소스로 이동](media/cognitive-services-apis-create-account/cog-serv-go-to-resource.png)

열리는 빠른 시작 창에서 끝점 및 키에 액세스할 수 있습니다.

![키 및 끝점 가져오기](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>가격 책정 계층 및 요금 청구

가격 책정 계층 및 청구 되는 금액은 인증 정보를 사용 하 여 전송 하는 트랜잭션 수를 기반으로 합니다. 각 가격 책정 계층은 다음을 지정 합니다.
* 초당 허용 되는 최대 트랜잭션 수 (TPS)입니다.
* 가격 책정 계층 내에서 사용 하도록 설정 된 서비스 기능입니다.
* 미리 정의 된 트랜잭션 수에 대 한 비용입니다. 이 용량을 초과 하면 서비스에 대 한 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) 에 지정 된 대로 추가 요금이 발생 합니다.

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제 하면 해당 그룹에 포함 된 다른 리소스도 모두 삭제 됩니다.

Azure Portal을 사용하여 리소스 그룹을 제거하려면

1. Azure Portal에서 왼쪽의 메뉴를 확장하여 서비스 메뉴를 열고 **리소스 그룹**을 선택하여 리소스 그룹 목록을 표시합니다.
2. 삭제할 리소스 그룹을 찾아 목록 오른쪽에 있는 자세히 단추 (...)를 마우스 오른쪽 단추로 클릭 합니다.
3. **리소스 그룹 삭제**를 선택하고 확인합니다.

## <a name="see-also"></a>참고자료

* [Azure Cognitive Services에 대한 요청 인증](authentication.md)
* [Azure Cognitive Services 이란?](Welcome.md)
* [자연어 지원](language-support.md)
* [Docker 컨테이너 지원](cognitive-services-container-support.md)
