---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996905"
---
>[!NOTE]
> 2019년 7월 1일 이후에 생성된 리소스의 엔드포인트는 아래에 표시된 사용자 지정 하위 도메인 형식을 사용합니다. 자세한 내용 및 지역별 엔드포인트의 전체 목록은 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)을 참조하세요. 

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. [Azure Portal](../../cognitive-services-apis-create-account.md)을 사용하여 잉크 인식기에 대한 리소스를 만듭니다. 

* 7일 동안 유효한 [평가판 키](https://azure.microsoft.com/try/cognitive-services/#decision)를 가져올 수도 있습니다. 등록 후 [Azure 웹 사이트](https://azure.microsoft.com/try/cognitive-services/my-apis/)에서 엔드포인트를 사용할 수 있습니다.

리소스를 만든 후 [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)에서 리소스를 열고 **빠른 시작**을 클릭하여 엔드포인트와 키를 가져옵니다.

두 개의 [환경 변수](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)를 만듭니다.

* `INK_RECOGNITION_SUBSCRIPTION_KEY` - 리소스에 대한 엔드포인트입니다. 다음과 같이 표시됩니다. <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT` - 요청을 인증하기 위한 구독 키입니다.   
