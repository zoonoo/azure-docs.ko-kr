---
title: Azure Portal에서 Cognitive Services API 계정 만들기
titlesuffix: Azure Cognitive Services
description: Azure Portal에서 Microsoft Cognitive Services API를 만드는 방법입니다.
services: cognitive-services
author: garyericson
manager: cgronlun
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: garye
ms.openlocfilehash: 37f53303a3b0c224c1286fb488a796fd5cdee0e5
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386419"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 Cognitive Services 계정 만들기

이 빠른 시작을 통해 Azure Cognitive Services 사용을 시작합니다. 이러한 서비스는 사용 가능한 하나 이상의 Cognitive Services API에 연결할 수 있도록 하는 Azure [리소스](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)로 표시됩니다.

## <a name="prerequisites"></a>필수 조건

* 유효한 Azure 구독. 무료로 [계정을 만들 수 있습니다](https://azure.microsoft.com/free/).

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Azure Cognitive Services 리소스 생성 및 구독

1. [Azure Portal](http://portal.azure.com)에 로그인한 후 **+리소스 만들기**를 클릭합니다.
    
    ![Cognitive Services API 선택](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. Azure Marketplace에서 **AI + Machine Learning**을 선택합니다. 관심 있는 서비스가 표시되지 않으면 **모두 보기**를 클릭하여 전체 Cognitive Services API 카탈로그를 표시합니다.

    ![Cognitive Services API 선택](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. **만들기** 페이지에서 다음 정보를 제공합니다.

    |    |    |
    |--|--|
    | **Name** | Cognitive Services 리소스를 설명하는 이름입니다. 설명이 포함된 이름(예: *MyNameFaceAPIAccount*)을 사용하는 것이 좋습니다. |
    | **구독** | 사용 가능한 Azure 구독 중 하나를 선택합니다. |
    | **위치**: | Cognitive Service 인스턴스의 위치입니다. 다른 위치를 사용하면 대기 시간이 발생할 수 있지만 리소스의 런타임 가용성에는 영향을 주지 않습니다. |
    | **가격 책정 계층** | Cognitive Services 계정의 비용은 선택한 옵션 및 사용량에 따라 다릅니다. 자세한 내용은 API [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/)를 참조하세요.
    | **리소스 그룹** | Cognitive Services 리소스를 포함할 [Azure 리소스 그룹](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group)입니다. 새 그룹을 만들거나 기존 그룹에 추가할 수 있습니다. |

    ![리소스 만들기 화면](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>리소스 액세스 

> [!NOTE]
> 구독 소유자는 [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition)을 적용하고, “허용되는 리소스 종류 없음” 정책 정의를 할당하고, **Microsoft.CognitiveServices/accounts**를 대상 리소스 종류로 지정하여 리소스 그룹 및 구독의 Cognitive Services 계정을 만들지 못하게 할 수 있습니다.

리소스를 만든 후 고정하면 Azure 대시보드에서 액세스할 수 있습니다. 그렇지 않으면 **리소스 그룹**에서 찾을 수 있습니다.

Cognitive Services 리소스 내에서 **개요** 섹션의 엔드포인트 URL 및 키를 사용하여 응용 프로그램에서 API 호출을 시작할 수 있습니다.

![리소스 화면](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Computer Vision API C# 자습서](https://docs.microsoft.com/azure/cognitive-services/computer-vision/tutorials/csharptutorial)

## <a name="see-also"></a>참고 항목

* [빠른 시작: 이미지에서 필기된 텍스트 추출](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [자습서: 이미지에서 얼굴을 감지하고 포착하는 앱 만들기](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Custom Search 웹 페이지 빌드](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Bot Framework를 사용하여 봇에 LUIS(Language Understanding) 통합](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
