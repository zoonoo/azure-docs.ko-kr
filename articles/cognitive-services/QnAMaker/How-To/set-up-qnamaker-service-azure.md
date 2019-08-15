---
title: QnA Maker 서비스 설정 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 기술 자료를 만들려면 먼저 Azure에서 QnA Maker 서비스를 설정해야 합니다. 구독에 새 리소스를 만들 수 있는 권한이 있으면 누구든지 QnA Maker 서비스를 설정할 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: f30b55eda4a02cfb3e961c0019128e4fe686cf53
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967663"
---
# <a name="create-a-qna-maker-service"></a>QnA Maker 서비스 만들기

QnA Maker 기술 자료를 만들려면 먼저 Azure에서 QnA Maker 서비스를 설정해야 합니다. 구독에 새 리소스를 만들 수 있는 권한이 있으면 누구든지 QnA Maker 서비스를 설정할 수 있습니다.

## <a name="create-a-new-service"></a>새 서비스 만들기

이 절차에서는 몇 가지 Azure 리소스를 배포 합니다. 이러한 리소스가 함께 작동하여 기술 자료 콘텐츠를 관리하고 엔드포인트를 통해 질문-답변 기능을 제공합니다.

1. Azure Portal에 로그인 하 여 QnA Maker 리소스를 [만듭니다](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. 사용 약관을 읽은 후 **만들기** 를 선택 합니다.

    ![새 QnA Maker 서비스 만들기](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. **QnA Maker**에서 적절한 계층과 지역을 선택합니다.

    ![새 QnA Maker 서비스 만들기 - 가격 책정 계층 및 지역](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * 이 QnA Maker 서비스를 식별하는 고유 이름으로 **이름**을 채웁니다. 이 이름은 기술 자료가 연결되는 QnA Maker 엔드포인트도 식별합니다.
    * QnA Maker 리소스가 배포되는 **구독**을 선택합니다.
    * QnA Maker 관리 서비스 (포털 및 관리 Api)에 대 한 **가격 책정 계층** 을 선택 합니다. SKU 가격 책정에 대한 자세한 내용은 [여기](https://aka.ms/qnamaker-pricing)를 참조하세요.
    * 새 **리소스 그룹**을 만들거나(권장), QnA Maker 리소스를 배포할 기존 리소스 그룹을 사용합니다. QnA Maker는 여러 Azure 리소스를 만듭니다. 이러한 리소스를 보유 하는 리소스 그룹을 만들 때 리소스 그룹 이름으로 이러한 리소스를 쉽게 찾고 관리 하 고 삭제할 수 있습니다.
    * **리소스 그룹 위치**를 선택 합니다.
    * Azure Search 서비스의 **검색 가격 책정 계층**을 선택합니다. 체험 계층 옵션이 회색으로 표시되는 경우 이미 구독에 체험 Azure Search 계층이 배포되어 있는 것입니다. 이 경우 기본 Azure Search 계층으로 시작해야 합니다. [여기](https://azure.microsoft.com/pricing/details/search/)에서 Azure Search 가격 책정 정보를 참조하세요.
    * Azure Search 데이터를 배포할 **검색 위치**를 선택합니다. 고객 데이터를 저장해야 하는 위치의 제한은 Azure Search에 대해 선택한 위치를 알립니다.
    * **앱 이름**에 App Service의 이름을 지정합니다.
    * 기본적으로 App Service의 기본값은 표준(S1) 계층입니다. 계획을 만든 후에 변경할 수 있습니다. [여기](https://azure.microsoft.com/pricing/details/app-service/)에서 App Service 가격 책정 정보를 참조하세요.
    * App Service를 배포할 **웹 사이트 위치**를 선택합니다.

        > [!NOTE]
        > 검색 위치는 웹 사이트 위치와 다를 수 있습니다.

    * **Application Insights**를 사용할지 여부를 선택합니다. **Application Insights**를 사용하도록 설정하면 QnA Maker는 트래픽, 채팅 로그 및 오류에 대한 원격 분석을 수집합니다.
    * Application Insights 리소스를 배포할 **App Insights 위치**를 선택합니다.
    * 비용 절감 액을 위해 QnA Maker에 대해 생성 되는 일부 Azure 리소스를 [공유할](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) 수 있습니다. 

1. 모든 필드의 유효성을 검사 한 후에는 **만들기** 를 선택 하 여 구독에서 이러한 서비스의 배포를 시작할 수 있습니다. 작업을 완료하는 데 몇 분 정도 걸립니다.

1. 배포가 완료되면 구독에 다음 리소스가 생성됩니다.

    ![QnA Maker 서비스를 새로 만든 리소스](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>관리 서비스 지역

초기 데이터 처리를 위해 포털 &에만 사용 되는 QnA Maker의 관리 서비스는 미국 서 부 에서만 사용할 수 있습니다. 이 미국 서 부 서비스에는 고객 데이터가 저장 되지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 만들기 및 게시](../Quickstarts/create-publish-knowledge-base.md)
