---
title: '빠른 시작: 기술 자료 만들기 - QnA Maker'
titleSuffix: Azure Cognitive Services
description: QnA Maker API 서비스 포털을 사용하여 잡담이 포함된 기술 자료를 만드는 옵션을 추가합니다. 이를 통해 앱을 더 개선할 수 있습니다. KB에서 사전에 채워지는 상단 잡담 세트를 추가하면 봇 잡담의 시작점 역할을 하며, 잡담을 처음부터 작성하는 데 드는 비용과 시간을 많이 절약할 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 798bbb725d4764c5ec7a1d69770e9508af0fdf5a
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376204"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>빠른 시작: QnA Maker API 서비스 포털을 사용하여 기술 자료 만들기

QnA Maker API 서비스 포털을 사용하면 기술 자료를 만들 때 간단하게 기존 데이터 원본을 추가할 수 있습니다. 다음 문서 형식에서 새 QnA Maker 기술 자료를 만들 수 있습니다.

<!-- added for scanability -->
* FAQ 페이지
* 제품 설명서
* 구조화된 문서

기술 자료에 사용자가 더 많이 참여하도록 잡담 개성을 포함시킵니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

## <a name="create-a-new-knowledge-base"></a>새 기술 자료 만들기

1. Azure 자격 증명을 사용하여 [QnA Maker 포털](https://qnamaker.ai)에 로그인하고 **기술 자료 문서 만들기**를 선택합니다.

1. 아직 QnA Maker 서비스를 만들지 않은 경우 **QnA 서비스 만들기**를 선택합니다. 

1. QnA Maker 포털의 **2단계**에서 목록의 QnA Maker 서비스와 연결된 Azure 테넌트, Azure 구독 이름 및 Azure 리소스 이름을 선택합니다. 기술 자료를 호스트할 Azure QnA Maker 서비스를 선택합니다.

    ![QnA 서비스 설정](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

1. 기술 자료의 이름 및 새 기술 자료에 대한 데이터 원본을 입력합니다.

    ![데이터 원본 설정](../media/qnamaker-how-to-create-kb/set-data-sources.png)

1. 서비스의 **이름**을 `my first kb`와 같이 지정합니다. 중복 이름 및 특수 문자가 지원됩니다.

1. QnA Maker 문제 해결 페이지를 URL(`https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/troubleshooting`)로 추가하고 `+ Add URL`을 선택합니다. 지원되는 원본 형식에 대한 자세한 내용은 [여기](../Concepts/data-sources-supported.md)서 확인할 수 있습니다. 이 빠른 시작에서는 추출하려는 데이터의 **파일을 업로드하지 않습니다**. 추가할 수 있는 문서 수는 [가격 책정 정보](https://aka.ms/qnamaker-pricing)를 참조하세요.

1. **‘전문가용’ 잡담**을 KB에 추가합니다.  

1. **KB 만들기**를 선택합니다.

    ![기술 자료 만들기](../media/qnamaker-how-to-create-kb/create-kb.png)

1. 데이터 추출을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

    ![추출](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. 기술 자료가 성공적으로 만들어지면 **기술 자료** 페이지로 리디렉션됩니다.

## <a name="clean-up-resources"></a>리소스 정리

기술 자료를 사용한 작업이 완료되면 QnA Maker 포털에서 제거합니다.

## <a name="next-steps"></a>다음 단계

비용 절감 측정값의 경우, QnA Maker용으로 생성된 일부(전체는 아님) Azure 리소스를 [공유](set-up-qnamaker-service-azure.md#share-existing-services-with-qna-maker)할 수 있습니다.

> [!div class="nextstepaction"]
> [메타데이터를 사용하여 질문 추가](../quickstarts/add-question-metadata-portal.md)
