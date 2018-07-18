---
title: 키 관리 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: QnA Maker 키를 관리하는 방법
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b402187f4949dac34fa476648c81b980ba3efc96
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376334"
---
# <a name="key-management"></a>키 관리

QnA Maker 서비스는 **구독 키**와 **끝점 키**의 두 종류 키를 사용합니다.

![키 관리](../media/qnamaker-how-to-key-management/key-management.png)

1. **구독 키**: 이러한 키는 [QnA Maker 관리 서비스 API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)에 액세스하는 데 사용됩니다. 이 API를 사용하면 기술 자료에 대해 다양한 CRUD 작업을 수행할 수 있습니다.  

2. **끝점 키**: 이러한 키는 사용자 질문에 대한 응답을 가져오기 위해 기술 자료 끝점에 액세스하는 데 사용됩니다. 일반적으로 QnA Maker 서비스를 이용하는 챗봇/앱 코드에서 이 끝점을 사용합니다.
 
## <a name="subscription-keys"></a>구독 키
QnA Maker 리소스를 만든 Azure Portal에서 구독 키를 보고 다시 설정할 수 있습니다. 
1. Azure Portal에서 QnA Maker 리소스로 이동합니다.

    ![QnA Maker 리소스 목록](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. **키**로 이동합니다.

    ![구독 키](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>끝점 키

끝점 키는 [QnA Maker 포털](https://qnamaker.ai)에서 관리할 수 있습니다.

1. [QnA Maker 포털](https://qnamaker.ai)에 로그인하고 **키 관리**로 이동합니다.

    ![끝점 키](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 키를 보거나 다시 설정합니다.

    ![끝점 키 관리자](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >손상된 것처럼 보이면 키를 새로 고칩니다. 앱/봇 코드에 해당 변경 내용을 적용해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [다른 언어로 된 기술 자료 만들기](./language-knowledge-base.md)
