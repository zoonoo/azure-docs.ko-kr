---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 156486f4f4f0df3d4bb4ab76492709bbecfb8eb5
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906447"
---
## <a name="set-up"></a>설정

### <a name="create-a-translator-text-resource"></a>Translator Text 리소스 만들기

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. 로컬 머신에서 [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 또는 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)를 사용하여 Translator Text용 리소스를 만듭니다. 또한 다음을 수행할 수 있습니다.

* 7일 동안 유효한 [평가판 키](https://azure.microsoft.com/try/cognitive-services)를 가져옵니다. 등록 후 Azure 웹 사이트에서 사용할 수 있습니다.
* [Azure Portal](https://portal.azure.com/)에서 기존 리소스를 봅니다.

평가판 구독 또는 리소스에서 키를 가져온 후 다음 두 가지 [환경 변수](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)를 만듭니다.

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` - Translator Text 리소스의 구독 키입니다.
* `TRANSLATOR_TEXT_ENDPOINT` - 리소스의 지역별 엔드포인트 또는 사용자 지정 하위 도메인 이름입니다.
