---
title: Speech Service 체험해 보기
titleSuffix: Azure Cognitive Services
description: 음성 서비스를 시작 하는 것은 쉽고 저렴 합니다. 무료로 사용할 수 있는 두 가지 옵션이 있으며, 서비스에서 수행할 수 있는 작업을 검색 하 고 요구 사항에 적합 한지 여부를 결정할 수 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 5dc1f8ed31c8d7199a31643a76e611d828da77ac
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056468"
---
# <a name="try-the-speech-service-for-free"></a>Speech Service 체험해 보기

이 문서에서는 서비스에서 수행할 수 있는 작업을 검색 하 고 요구 사항에 적합 한지 여부를 결정할 수 있도록 음성 서비스를 무료로 쉽게 테스트 하는 옵션을 선택 합니다. 상황 및 사용 사례에 따라 다음 두 가지 옵션 중 하나를 선택 합니다.

- [옵션 1](#no-card): 신용 카드 정보를 제공 하지 않고 **무료 평가판** API 키를 즉시 가져옵니다 (기존 Azure 계정이 있어야 함). **무료 평가판** 은 30 일 동안 지속 되 고 데이터는 끝에 삭제 됩니다. 이 옵션은 서비스를 신속 하 게 실험 하는 데 가장 적합 합니다.
- [옵션 2](#new-resource): **무료 구독** (신용 카드 정보 필요)을 사용 하 여 무료로 Azure에서 새 음성 리소스를 만듭니다. **무료 구독** 에는 주로 유료 구독 보다 더 엄격한 요금 제한이 있습니다. 이 옵션은 서비스를 테스트 하 고 나중에 유료 구독으로 업그레이드 하 고 데이터를 잃지 않으려는 경우에 가장 적합 합니다.

## <a name="try-the-speech-service-without-credit-card-info"></a><a id="no-card"></a>신용 카드 정보 없이 음성 서비스를 사용해 보세요.

30 일 무료 평가판을 활성화 하 고 API 키를 가져오려면 다음 단계를 완료 합니다. 다음 단계가 완료 되 면 즉시 평가 기간이 시작 됩니다.

1. [인식 서비스 시도](https://azure.microsoft.com/try/cognitive-services/)로 이동 합니다.
1. **Speech API** 탭을 선택합니다.
1. **API 키 가져오기**를 선택 합니다.

청구 선택 항목이 표시 됩니다. 무료 옵션을 선택한 다음 사용자 계약을 읽고 승인 합니다. 30 일 동안 음성 서비스를 무료로 사용 하는 데 사용할 수 있는 키가 제공 됩니다.

## <a name="try-the-speech-service-by-creating-an-azure-resource"></a><a id="new-resource"></a>Azure 리소스를 만들어 Speech service를 사용해 보세요.

다음 단계를 수행 하려면 Microsoft 계정 및 Azure 계정이 모두 필요 합니다. Microsoft 계정 없는 경우에는 [Microsoft 계정 포털](https://account.microsoft.com/account)에서 무료로 등록할 수 있습니다. **Microsoft에 로그인** 선택 하 고 로그인 하 라는 메시지가 표시 되 면 **Microsoft 계정 만들기**를 선택 합니다. 단계에 따라 새 Microsoft 계정을 만들고 확인합니다.

Microsoft 계정 있으면 [Azure 등록 페이지로](https://azure.microsoft.com/free/ai/)이동 하 여 **무료 시작**을 선택 하 고 Microsoft 계정를 사용 하 여 새 Azure 계정을 만듭니다.

> [!NOTE]
> 음성 서비스에는 두 가지 서비스 계층 (무료 및 구독)이 있습니다. 여기에는 서로 다른 제한과 이점이 있습니다. 무료 Azure 계정에 등록 하는 경우 유료 음성 서비스 구독에 적용 될 수 있는 $200 서비스 크레딧을 제공 하며, 최대 30 일 동안 유효 합니다.
>
> 무료, 낮은 볼륨 음성 서비스 계층을 사용 하는 경우 무료 평가판 또는 서비스 크레딧이 만료 된 후에도이 무료 구독을 유지할 수 있습니다.
>
> 자세한 내용은 [Cognitive Services 가격-음성 서비스](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)를 참조 하세요.

### <a name="create-the-resource"></a>리소스 만들기

Speech 서비스 리소스(체험 또는 유료 계층)를 Azure 계정에 추가하려면 다음을 수행합니다.

1. Microsoft 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 포털의 왼쪽 위에서 **리소스 만들기**를 선택합니다. **리소스 만들기**가 표시 되지 않으면 왼쪽 위에서 축소 된 메뉴를 선택 하 여 언제 든 지 리소스를 찾을 수 있습니다.

   ![축소 된 탐색 단추](media/index/collapsed-nav.png)

1. **새** 창에서 검색 상자에 "speech"를 입력 하 고 enter 키를 누릅니다.

1. 검색 결과에서 **Speech**를 선택합니다.

   ![음성 검색 결과](media/index/speech-search.png)

1. **만들기**를 선택 하 고 다음을 선택 합니다.

   - 새 리소스에 대 한 고유한 이름을 지정 합니다. 이 이름은 동일한 서비스에 연결 된 여러 구독을 구분 하는 데 도움이 됩니다.
   - 새 리소스가 연결되는 Azure 구독을 선택하여 요금이 얼마나 청구되는지 확인합니다.
   - 리소스가 사용 될 [지역을](regions.md) 선택 합니다.
   - 무료 (F0) 또는 유료 (S0) 가격 책정 계층을 선택 합니다. 각 계층에 대 한 가격 책정 및 사용 할당량에 대 한 전체 정보를 **보려면 전체 가격 책정 정보 보기**를 선택 합니다.
   - 이 Speech 구독에 대한 새 리소스 그룹을 만들거나 기존 리소스 그룹에 해당 구독을 할당합니다. 리소스 그룹은 다양한 Azure 구독의 구성을 유지하는 데 도움이 됩니다.
   - **만들기**를 선택합니다. 그러면 배포 개요로 이동 하 여 배포 진행률 메시지를 표시 합니다.

> [!NOTE]
> 표준 계층 구독은 하나 이상의 지역에서 무제한으로 만들 수 있지만, 체험 계층 구독은 하나만 만들 수 있습니다. 7 일 동안 사용 되지 않는 상태로 유지 되는 무료 계층의 모델 배포는 자동으로 서비스 해제 됩니다.

새 음성 리소스를 배포 하는 데 몇 분 정도 걸립니다. 배포가 완료 되 면 **리소스로 이동** 을 선택 하 고 왼쪽 탐색 창에서 **키** 를 선택 하 여 음성 서비스 구독 키를 표시 합니다. 각 구독에는 두 개의 키가 있으며, 애플리케이션에서 두 키 중 하나를 사용할 수 있습니다. 키를 코드 편집기 또는 다른 위치에 빠르게 복사 하 고 붙여넣으려면 각 키 옆의 복사 단추를 선택 하 고 클립보드 내용을 원하는 위치에 붙여 넣으려면 windows를 전환 합니다.

> [!IMPORTANT]
> 이러한 구독 키는 인식 서비스 API에 액세스 하는 데 사용 됩니다. 키를 공유 하지 마세요. 예를 들어 Azure Key Vault를 사용 하 여 안전 하 게 저장 합니다. 또한 이러한 키를 정기적으로 다시 생성 하는 것이 좋습니다. API 호출을 수행 하는 데는 키가 하나만 필요 합니다. 첫 번째 키를 다시 생성 하는 경우 두 번째 키를 사용 하 여 서비스에 계속 액세스할 수 있습니다.

## <a name="switch-to-a-new-subscription"></a>새 구독으로 전환

평가판이 만료되거나 애플리케이션을 게시할 때와 같이 한 구독에서 다른 구독으로 전환하려면 코드의 지역과 구독 키를 새 Azure 리소스의 지역과 구독 키로 바꿉니다.

## <a name="about-regions"></a>영역 정보

- 애플리케이션에서 [Speech SDK](speech-sdk.md)를 사용하는 경우 Speech 구성을 만들 때 `westus`와 같은 지역 코드를 제공합니다.
- 애플리케이션에서 Speech 서비스의 [REST API](rest-apis.md) 중 하나를 사용하는 경우 지역은 요청 시 사용하는 엔드포인트 URI의 일부가 됩니다.
- 지역에 대해 만든 키는 해당 지역에서만 유효합니다. 다른 지역에서 사용하려고 하면 인증 오류가 발생합니다.

## <a name="next-steps"></a>다음 단계

10분 빠른 시작 중 하나를 수행하거나 SDK 샘플을 확인합니다.

> [!div class="nextstepaction"]
> [빠른 시작: 음성 CLI](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programmer-tool-spx) 
>  를 사용 하 여 음성 인식 [SPEECH SDK 샘플](speech-sdk.md#sample-source-code)
