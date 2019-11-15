---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 2fe3104d61b5fe2fbf9624ed2fd4fdb2de5686a2
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750186"
---
애플리케이션을 인증하는 키와 엔드포인트를 가져옵니다. 로컬 머신에서 [Azure Portal](../../../cognitive-services-apis-create-account.md) 또는 [Azure CLI](../../../cognitive-services-apis-create-account-cli.md)를 사용하여 Text Analytics용 리소스를 만듭니다. 또한 다음을 수행할 수 있습니다.

* 7일 동안 유효한 [평가판 키](https://azure.microsoft.com/try/cognitive-services/#decision)를 가져옵니다. 키를 등록 후 [Azure 웹 사이트](https://azure.microsoft.com/try/cognitive-services/my-apis/)에서 사용할 수 있습니다.  
* [Azure Portal](https://portal.azure.com/)에서 리소스를 확인합니다.

평가판 구독 또는 리소스에서 키와 엔드포인트를 가져온 후 두 가지 [환경 변수](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)를 만듭니다. 하나는 키에 대해 이름이 지정된 `TEXT_ANALYTICS_SUBSCRIPTION_KEY`이고, 다른 하나는 엔드포인트에 대해 이름이 지정된 `TEXT_ANALYTICS_ENDPOINT`입니다.