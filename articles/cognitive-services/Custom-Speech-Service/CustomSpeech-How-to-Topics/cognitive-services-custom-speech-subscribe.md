---
title: Azure의 Custom Speech Service에 대한 구독 키 가져오기 | Microsoft Docs
description: Cognitive Services에서 Custom Speech Service 호출에 대한 구독 키를 가져오는 방법을 알아봅니다.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 84ef657af2cc3dc4a7168a815b5e51d6f4f33fd7
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338372"
---
# <a name="obtain-subscription-keys"></a>구독 키 얻기

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Azure Custom Speech Service를 사용하려면 먼저 사용자 계정을 Azure 구독에 연결해야 합니다. 무료 계층과 유료 계층의 두 가지 구독이 제공됩니다. 계층에 대한 정보는 [가격 책정 페이지](https://www.microsoft.com/cognitive-services/en-us/pricing)를 참조하세요.

## <a name="get-a-subscription-key"></a>구독 키 가져오기
1. 다음 두 가지 방법 중 하나로 Azure Portal에서 구독 키를 가져올 수 있습니다.

    * [Azure Portal](https://ms.portal.azure.com)로 이동하고, _Cognitive Services_ 를 검색하여 새 Cognitive Services API를 추가한 다음, **Cognitive Services APIs**를 선택합니다.

      ![Cognitive Services 검색](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * 또는 바로 [Cognitive Services API](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices)로 이동합니다.

        ![Cognitive Services API](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
1. 다음 필수 필드에 정보를 입력합니다.

      a. **계정 이름**. 적합한 이름을 사용합니다. 리소스 목록에서 Cognitive Services 구독을 찾을 수 있도록 이 이름을 기억해 두세요.

      b. **구독**. Azure 구독에서 하나를 선택합니다.

      다. **API 형식**. **Custom Speech Service(미리 보기)** 를 선택합니다.

      d. **위치** - 현재는 **미국 서부**입니다.

      e. **가격 책정 계층**. 적절한 계층을 선택합니다. **F0**은 평가판 계층입니다. 허용되는 할당량은 [가격 책정 페이지](https://www.microsoft.com/cognitive-services/en-us/pricing)에 설명되어 있습니다.

      ![Cognitive Services 계정 만들기](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

1. 리소스 목록에서 대시보드의 보기 또는 입력한 계정 이름으로 된 서비스를 찾을 수 있을 것입니다. 서비스를 선택하면 해당 서비스에 대한 개요를 볼 수 있습니다. 왼쪽 목록의 **리소스 관리**에서 **키**를 선택합니다. **키 1**을 복사합니다.

      다음 단계에서 이 구독 키가 필요합니다.

      ![키 1](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > 개요 페이지의 **구독 ID**는 복사하지 마세요. 구독 키는 그 다음 단계에 필요합니다.
      >

      ![개요 구독 ID](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

1. 구독 키를 입력하려면 오른쪽 위에 있는 리본에서 사용자 계정을 선택합니다. 드롭다운 메뉴에서 **구독**을 선택합니다.

      ![구독 메뉴 항목](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    구독 표가 나타나는데, 처음으로 열 때는 비어 있습니다.

    ![구독 표](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

1. **새로 추가**를 선택합니다. 구독 및 구독 키의 이름을 입력합니다. 구독의 **키 1**(기본 키) 또는 **키 2**(보조 키)입니다.

      ![구독 키 이름](../../../media/cognitive-services/custom-speech-service/custom-speech-enter-subsciption.png)

데이터를 업로드하고, 모델을 교육하고, 배포를 수행하려면 Custom Speech Service 작업을 Azure 구독에 연결해야 합니다. 무료 계층 또는 유료 계층 구독일 수 있습니다. 자세한 내용은 [가격 책정 페이지](https://www.microsoft.com/cognitive-services/en-us/pricing)를 참조하세요.

## <a name="get-a-subscription-id"></a>구독 ID 가져오기
구독 ID를 가져오려면 Azure Portal로 이동합니다. *Cognitive Services* 및 *Custom Speech Service*를 검색하고, 지침을 따릅니다.

> [!NOTE]
> 구독 키는 이 프로세스의 뒷부분에서 필요합니다.
>

## <a name="next-steps"></a>다음 단계
* [사용자 지정 음향 모델](cognitive-services-custom-speech-create-acoustic-model.md) 만들기를 시작합니다.
* [사용자 지정 언어 모델](cognitive-services-custom-speech-create-language-model.md) 만들기를 시작합니다.
