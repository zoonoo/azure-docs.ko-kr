---
title: Speech Service 체험해 보기
titleSuffix: Azure Cognitive Services
description: 음성 서비스를 쉽고 저렴하게 시작할 수 있습니다. 서비스가 무엇을 할 수 있는지 알아보고 필요에 맞는지 여부를 결정할 수 있도록 무료로 사용할 수 있는 두 가지 옵션이 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: f74e3ea3d20ad2666b434e009cf62add6f88d200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221270"
---
# <a name="try-the-speech-service-for-free"></a>Speech Service 체험해 보기

이 문서에서는 서비스가 수행할 수 있는 작업을 검색하고 사용자의 요구에 적합한지 여부를 결정할 수 있도록 음성 서비스를 무료로 쉽게 테스트하는 옵션을 선택합니다. 상황 및 사용 사례에 따라 다음 두 가지 옵션 중 하나를 선택합니다.

- [옵션 1](#no-card): 신용 카드 정보를 제공하지 않고 **즉시 무료 평가판** API 키를 얻을 수 있습니다 (기존 Azure 계정이 있어야합니다). **무료 평가판은** 30일 간 지속되며 데이터가 끝나면 삭제됩니다. 이 옵션은 서비스에 대한 빠른 실험에 가장 적합합니다.
- [옵션 2](#new-resource): **무료 구독(신용** 카드 정보 필요)을 사용하여 Azure에서 새 음성 리소스를 무료로 만듭니다. **무료 구독은** 주로 유료 구독보다 더 엄격한 요금 제한이 있습니다. 이 옵션은 서비스를 테스트하고 나중에 유료 구독으로 업그레이드할 계획이며 데이터를 잃고 싶지 않은 경우에 가장 적합합니다.

## <a name="try-the-speech-service-without-credit-card-info"></a><a id="no-card"></a>신용 카드 정보 없이 음성 서비스를 사용해 보십시오.

다음 단계를 완료하여 30일 무료 평가판을 활성화하고 API 키를 가져옵니다. 평가판 기간은 다음 단계가 완료되면 즉시 시작됩니다.

1. 인지 [서비스를 시도하십시오.](https://azure.microsoft.com/try/cognitive-services/)
1. **Speech API** 탭을 선택합니다.
1. **API 키 받기를 선택합니다.**

결제 선택 항목이 표시됩니다. 무료 옵션을 선택한 다음 사용자 동의서를 읽고 승인합니다. 30일 동안 음성 서비스를 무료로 시도하는 데 사용할 수 있는 키가 제공됩니다.

## <a name="try-the-speech-service-by-creating-an-azure-resource"></a><a id="new-resource"></a>Azure 리소스를 만들어 음성 서비스를 사용해 보십시오.

다음 단계의 경우 Microsoft 계정과 Azure 계정이 모두 필요합니다. Microsoft 계정이 없는 경우 [Microsoft 계정 포털에서](https://account.microsoft.com/account)무료로 가입할 수 있습니다. **Microsoft에서 로그인을** 선택한 다음 로그인하라는 메시지가 표시되면 **Microsoft 계정 만들기를**선택합니다. 단계에 따라 새 Microsoft 계정을 만들고 확인합니다.

Microsoft 계정이 있으면 Azure 등록 [페이지로](https://azure.microsoft.com/free/ai/)이동하여 **무료 시작을**선택하고 Microsoft 계정을 사용하여 새 Azure 계정을 만듭니다.

> [!NOTE]
> 음성 서비스에는 두 가지 서비스 계층이 있습니다: 무료 및 구독, 서로 다른 제한 및 혜택이 있습니다. 무료 Azure 계정에 가입하면 최대 30일 동안 유효한 유료 음성 서비스 구독에 적용할 수 있는 서비스 크레딧이 $200입니다.
>
> 무료 저용량 음성 서비스 계층을 사용하는 경우 무료 평가판 또는 서비스 크레딧이 만료된 후에도 이 무료 구독을 유지할 수 있습니다.
>
> 자세한 내용은 [코그너티브 서비스 가격 - 음성 서비스를](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)참조하십시오.

### <a name="create-the-resource"></a>리소스 만들기

Speech 서비스 리소스(체험 또는 유료 계층)를 Azure 계정에 추가하려면 다음을 수행합니다.

1. Microsoft 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 포털의 왼쪽 위에서 **리소스 만들기**를 선택합니다. **리소스 만들기가**표시되지 않으면 왼쪽 상단에 있는 축소된 메뉴를 선택하여 언제든지 찾을 수 있습니다.

   ![축소된 탐색 단추](media/index/collapsed-nav.png)

1. **새** 창에서 검색 상자에 "음성"을 입력하고 ENTER를 누릅니다.

1. 검색 결과에서 **Speech**를 선택합니다.

   ![음성 검색 결과](media/index/speech-search.png)

1. **다음 을**선택합니다.

   - 새 리소스에 고유한 이름을 지정합니다. 이름은 동일한 서비스에 연결된 여러 구독을 구분하는 데 도움이 됩니다.
   - 새 리소스가 연결되는 Azure 구독을 선택하여 요금이 얼마나 청구되는지 확인합니다.
   - 리소스가 사용될 [지역을](regions.md) 선택합니다.
   - 무료(F0) 또는 유료(S0) 가격 등급 중 하나를 선택합니다. 각 계층의 가격 책정 및 사용량 할당량에 대한 전체 정보를 보려면 **전체 가격 세부 정보 보기를**선택합니다.
   - 이 Speech 구독에 대한 새 리소스 그룹을 만들거나 기존 리소스 그룹에 해당 구독을 할당합니다. 리소스 그룹은 다양한 Azure 구독의 구성을 유지하는 데 도움이 됩니다.
   - **만들기**를 선택합니다. 이렇게 하면 배포 개요로 이동하여 배포 진행 률 메시지를 표시합니다.

> [!NOTE]
> 표준 계층 구독은 하나 이상의 지역에서 무제한으로 만들 수 있지만, 체험 계층 구독은 하나만 만들 수 있습니다. 7일 동안 사용되지 않은 프리 티어의 모델 배포는 자동으로 해제됩니다.

새 음성 리소스를 배포하는 데 몇 분 정도 걸립니다. 배포가 완료되면 **리소스로 이동을** 선택하고 왼쪽 탐색 창에서 **키를** 선택하여 Speech 서비스 구독 키를 표시합니다. 각 구독에는 두 개의 키가 있으며, 애플리케이션에서 두 키 중 하나를 사용할 수 있습니다. 코드 편집기 또는 다른 위치에 키를 빠르게 복사/붙여넣려면 각 키 옆에 있는 복사 버튼을 선택하고 창을 전환하여 클립보드 내용을 원하는 위치에 붙여넣습니다.

> [!IMPORTANT]
> 이러한 구독 키는 코그너티브 서비스 API에 액세스하는 데 사용됩니다. 키를 공유하지 마십시오. 예를 들어 Azure 키 자격 증명 모음을 사용하여 안전하게 저장합니다. 또한 이러한 키를 정기적으로 다시 생성하는 것이 좋습니다. API 호출을 하려면 하나의 키만 필요합니다. 첫 번째 키를 다시 생성할 때 서비스에 대한 지속적인 액세스를 위해 두 번째 키를 사용할 수 있습니다.

## <a name="switch-to-a-new-subscription"></a>새 구독으로 전환

평가판이 만료되거나 애플리케이션을 게시할 때와 같이 한 구독에서 다른 구독으로 전환하려면 코드의 지역과 구독 키를 새 Azure 리소스의 지역과 구독 키로 바꿉니다.

## <a name="about-regions"></a>지역 소개

- 애플리케이션에서 [Speech SDK](speech-sdk.md)를 사용하는 경우 Speech 구성을 만들 때 `westus`와 같은 지역 코드를 제공합니다.
- 애플리케이션에서 Speech 서비스의 [REST API](rest-apis.md) 중 하나를 사용하는 경우 지역은 요청 시 사용하는 엔드포인트 URI의 일부가 됩니다.
- 지역에 대해 만든 키는 해당 지역에서만 유효합니다. 다른 지역에서 사용하려고 하면 인증 오류가 발생합니다.

## <a name="next-steps"></a>다음 단계

10분 빠른 시작 중 하나를 수행하거나 SDK 샘플을 확인합니다.

> [!div class="nextstepaction"]
> [빠른 시작: C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [음성 SDK 샘플에서 음성](speech-sdk.md#get-the-samples) 인식
