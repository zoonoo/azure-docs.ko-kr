---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 54ee19f3e278b424384ff55d7065713584235df1
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86144222"
---
## <a name="set-up"></a>설정

### <a name="create-a-translator-resource"></a>Translator 리소스 만들기

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. 로컬 머신에서 [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 또는 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)를 사용하여 Translator용 리소스를 만듭니다. 다음도 가능합니다.

* [Azure Portal](https://portal.azure.com/)에서 기존 리소스를 봅니다.

평가판 구독 또는 리소스에서 키를 가져온 후 다음 두 가지 [환경 변수](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)를 만듭니다.

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` - Translator 리소스의 구독 키입니다.
* `TRANSLATOR_TEXT_ENDPOINT` - Translator의 글로벌 엔드포인트입니다. 대신 `https://api.cognitive.microsofttranslator.com/`를
