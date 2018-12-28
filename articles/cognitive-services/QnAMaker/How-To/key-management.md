---
title: 리소스 및 키 관리 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 서비스는 구독 키와 엔드포인트 키의 두 종류 키를 사용합니다.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/26/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 25b23d280aca9ef13b8820596686a1f9dbecd2a1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085946"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>QnA Maker에서 키를 관리하는 방법

QnA Maker 서비스는 **구독 키**와 **엔드포인트 키**의 두 종류 키를 사용합니다.

![키 관리](../media/qnamaker-how-to-key-management/key-management.png)

1. **구독 키**: 이러한 키는 [QnA Maker 관리 서비스 API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)에 액세스하는 데 사용됩니다. 이 API를 사용하면 기술 자료에 대해 다양한 CRUD 작업을 수행할 수 있습니다.  

2. **엔드포인트 키**: 이러한 키는 사용자 질문에 대한 응답을 가져오기 위해 기술 자료 엔드포인트에 액세스하는 데 사용됩니다. 일반적으로 QnA Maker 서비스를 이용하는 챗봇/앱 코드에서 이 엔드포인트를 사용합니다.
 
## <a name="subscription-keys"></a>구독 키
QnA Maker 리소스를 만든 Azure Portal에서 구독 키를 보고 다시 설정할 수 있습니다. 
1. Azure Portal에서 QnA Maker 리소스로 이동합니다.

    ![QnA Maker 리소스 목록](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. **키**로 이동합니다.

    ![구독 키](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>엔드포인트 키

엔드포인트 키는 [QnA Maker 포털](https://qnamaker.ai)에서 관리할 수 있습니다.

1. [QnA Maker 포털](https://qnamaker.ai)에 로그인하고 **키 관리**로 이동합니다.

    ![엔드포인트 키](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 키를 보거나 다시 설정합니다.

    ![엔드포인트 키 관리자](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >손상된 것처럼 보이면 키를 새로 고칩니다. 앱/봇 코드에 해당 변경 내용을 적용해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [다른 언어로 된 기술 자료 만들기](./language-knowledge-base.md)
