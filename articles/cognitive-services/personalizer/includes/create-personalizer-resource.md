---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 11abd52681d7c9962af4e5bf0728f97b256223c1
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122818"
---
## <a name="create-a-personalizer-azure-resource"></a>Personalizer Azure 리소스 만들기

로컬 머신에서 [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 또는 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)를 사용하여 Personalizer용 리소스를 만듭니다. 다음도 가능합니다.

* 7일 동안 유효한 [평가판 키](https://azure.microsoft.com/try/cognitive-services)를 가져옵니다. 등록 후 [Azure 웹 사이트](https://azure.microsoft.com/try/cognitive-services/my-apis/)에서 사용할 수 있습니다.
* [Azure Portal](https://portal.azure.com/)에서 리소스를 확인합니다.

평가판 구독 또는 리소스에서 키를 가져오면 다음 두 가지 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

* 리소스 키에 대한 `PERSONALIZER_RESOURCE_KEY`
* 리소스 엔드포인트에 대한 `PERSONALIZER_RESOURCE_ENDPOINT`

Azure Portal의 **빠른 시작** 페이지에서 키와 엔드포인트 값을 모두 확인할 수 있습니다.
