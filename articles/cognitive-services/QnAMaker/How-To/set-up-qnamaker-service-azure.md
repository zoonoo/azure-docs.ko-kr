---
title: QnA Maker 서비스를 설정하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: QnA Maker 서비스를 설정하는 방법
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: ce452dd686529e017b4eae4717eadb044b389409
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376319"
---
# <a name="create-a-qna-maker-service"></a>QnA Maker 서비스 만들기

QnA Maker 기술 자료를 만들려면 먼저 Azure에서 QnA Maker 서비스를 설정해야 합니다. 구독에 새 리소스를 만들 수 있는 권한이 있으면 누구든지 QnA Maker 서비스를 설정할 수 있습니다.

이 설정은 몇 개의 Azure 리소스를 배포합니다. 이러한 리소스가 함께 작동하여 기술 자료 콘텐츠를 관리하고 끝점을 통해 질문-답변 기능을 제공합니다.

1. [Azure 포털](<https://portal.azure.com>) 에 로그인합니다.

2.  **새 리소스 추가**를 클릭하고 검색에 “qna maker”를 입력한 다음, QnA Maker 리소스를 선택합니다.

    ![새 QnA Maker 서비스 만들기](../media/qnamaker-how-to-setup-service/create-new-resource.png)

3.  사용 약관을 읽은 후 **만들기**를 클릭합니다.

    ![새 QnA Maker 서비스 만들기](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

4. **QnA Maker**에서 적절한 계층과 지역을 선택합니다.

    ![새 QnA Maker 서비스 만들기](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * 이 QnA Maker 서비스를 식별하는 고유 이름으로 **이름**을 채웁니다. 이 이름은 기술 자료가 연결되는 QnA Maker 끝점도 식별합니다.
    * QnA Maker 리소스가 배포되는 **구독**을 선택합니다.
    * QnA Maker 관리 서비스(포털 및 관리 API)에 대한 **관리 가격 책정 계층**을 선택합니다. SKU 가격 책정에 대한 자세한 내용은 [여기](https://aka.ms/qnamaker-pricing)를 참조하세요.
    * 새 **리소스 그룹**을 만들거나(권장), QnA Maker 리소스를 배포할 기존 리소스 그룹을 사용합니다.
    * Azure Search 서비스의 **검색 가격 책정 계층**을 선택합니다. 체험 계층 옵션이 회색으로 표시되는 경우 이미 구독에 체험 Azure Search 계층이 배포되어 있는 것입니다. 이 경우 기본 Azure Search 계층으로 시작해야 합니다. [여기](https://azure.microsoft.com/en-us/pricing/details/search/)에서 Azure Search 가격 책정 정보를 참조하세요.
    * Azure Search 데이터를 배포할 **검색 위치**를 선택합니다. 고객 데이터를 저장해야 하는 위치의 제한은 Azure Search에 대해 선택한 위치를 알립니다.
    * **앱 이름**에 App Service의 이름을 지정합니다.
    * 기본적으로 App Service의 기본값은 표준(S1) 계층입니다. 계획을 만든 후에 변경할 수 있습니다. [여기](https://azure.microsoft.com/en-in/pricing/details/app-service/)에서 App Service 가격 책정 정보를 참조하세요.
    * App Service를 배포할 **웹 사이트 위치**를 선택합니다.

        > [!NOTE]
        > 검색 위치는 웹 사이트 위치와 다를 수 있습니다.

    * **Application Insights**를 사용할지 여부를 선택합니다. **Application Insights**를 사용하도록 설정하면 QnA Maker는 트래픽, 채팅 로그 및 오류에 대한 원격 분석을 수집합니다.
    * Application Insights 리소스를 배포할 **App Insights 위치**를 선택합니다.

5. 모든 필드의 유효성이 검사되면 **만들기**를 클릭하여 구독에서 이러한 서비스의 배포를 시작할 수 있습니다. 작업을 완료하는 데 몇 분 정도 걸립니다.

6.  배포가 완료되면 구독에 다음 리소스가 생성됩니다.

    ![새 QnA Maker 서비스 만들기](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 만들기 및 게시](../Quickstarts/create-publish-knowledge-base.md)