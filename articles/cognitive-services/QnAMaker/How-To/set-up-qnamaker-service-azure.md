---
title: QnA Maker 서비스 설정 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 기술 자료를 만들려면 먼저 Azure에서 QnA Maker 서비스를 설정해야 합니다. 구독에 새 리소스를 만들 수 있는 권한이 있으면 누구든지 QnA Maker 서비스를 설정할 수 있습니다.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/02/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 90e7abf229e0ee1ca396150fee0c1f0970d257aa
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58876404"
---
# <a name="create-a-qna-maker-service"></a>QnA Maker 서비스 만들기

QnA Maker 기술 자료를 만들려면 먼저 Azure에서 QnA Maker 서비스를 설정해야 합니다. 구독에 새 리소스를 만들 수 있는 권한이 있으면 누구든지 QnA Maker 서비스를 설정할 수 있습니다.

## <a name="create-a-new-service"></a>새 서비스 만들기

이 절차는 몇 가지 Azure 리소스를 배포합니다. 이러한 리소스가 함께 작동하여 기술 자료 콘텐츠를 관리하고 엔드포인트를 통해 질문-답변 기능을 제공합니다.

1. [Azure Portal](<https://portal.azure.com>)에 로그인합니다.

1. 선택 **새 리소스 추가**, 검색에서 "qna maker"를 입력 하 고 QnA Maker 리소스 선택

    ![새 QnA Maker 서비스 만들기 - 새 리소스 추가](../media/qnamaker-how-to-setup-service/create-new-resource.png)

1. 선택 **만들기** 사용 약관을 읽은 후 합니다.

    ![새 QnA Maker 서비스 만들기](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. **QnA Maker**에서 적절한 계층과 지역을 선택합니다.

    ![새 QnA Maker 서비스 만들기 - 가격 책정 계층 및 지역](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * 이 QnA Maker 서비스를 식별하는 고유 이름으로 **이름**을 채웁니다. 이 이름은 기술 자료가 연결되는 QnA Maker 엔드포인트도 식별합니다.
    * QnA Maker 리소스가 배포되는 **구독**을 선택합니다.
    * QnA Maker 관리 서비스(포털 및 관리 API)에 대한 **관리 가격 책정 계층**을 선택합니다. SKU 가격 책정에 대한 자세한 내용은 [여기](https://aka.ms/qnamaker-pricing)를 참조하세요.
    * 새 **리소스 그룹**을 만들거나(권장), QnA Maker 리소스를 배포할 기존 리소스 그룹을 사용합니다. QnA Maker 여러 Azure 리소스를 만듭니다. 만들 때 이러한 리소스를 보유 하는 리소스 그룹 관리 및 리소스 그룹 이름으로 이러한 리소스를 삭제 합니다. 쉽게 찾을 수 있습니다.
    * Azure Search 서비스의 **검색 가격 책정 계층**을 선택합니다. 체험 계층 옵션이 회색으로 표시되는 경우 이미 구독에 체험 Azure Search 계층이 배포되어 있는 것입니다. 이 경우 기본 Azure Search 계층으로 시작해야 합니다. [여기](https://azure.microsoft.com/pricing/details/search/)에서 Azure Search 가격 책정 정보를 참조하세요.
    * Azure Search 데이터를 배포할 **검색 위치**를 선택합니다. 고객 데이터를 저장해야 하는 위치의 제한은 Azure Search에 대해 선택한 위치를 알립니다.
    * **앱 이름**에 App Service의 이름을 지정합니다.
    * 기본적으로 App Service의 기본값은 표준(S1) 계층입니다. 계획을 만든 후에 변경할 수 있습니다. [여기](https://azure.microsoft.com/pricing/details/app-service/)에서 App Service 가격 책정 정보를 참조하세요.
    * App Service를 배포할 **웹 사이트 위치**를 선택합니다.

        > [!NOTE]
        > 검색 위치는 웹 사이트 위치와 다를 수 있습니다.

    * **Application Insights**를 사용할지 여부를 선택합니다. **Application Insights**를 사용하도록 설정하면 QnA Maker는 트래픽, 채팅 로그 및 오류에 대한 원격 분석을 수집합니다.
    * Application Insights 리소스를 배포할 **App Insights 위치**를 선택합니다.
    * 비용 절감 측정값을 할 수 있습니다 [공유](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) QnA Maker에 대 한 만든 전부는 아니지만 일부 Azure 리소스입니다. 

1. 모든 필드는 유효성이 확인 되 면 선택할 수 있습니다 **만들기** 구독에서 이러한 서비스의 배포를 시작 합니다. 작업을 완료하는 데 몇 분 정도 걸립니다.

1. 배포가 완료되면 구독에 다음 리소스가 생성됩니다.

    ![QnA Maker 서비스를 새로 만든 리소스](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>관리 서비스의 지역

QnA Maker만 사용 되는 포털에 대 한 초기 데이터 처리에 대 한 관리 서비스는 미국 서 부 에서만 사용할 수 있습니다. 이 미국 서 부 서비스에서 고객 데이터가 저장 됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 만들기 및 게시](../Quickstarts/create-publish-knowledge-base.md)
