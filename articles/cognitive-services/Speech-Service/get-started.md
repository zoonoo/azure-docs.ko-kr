---
title: Speech Service 체험해 보기
description: Speech Service를 체험해 보는 방법을 알아봅니다.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 09/24/2018
ms.author: v-jerkin
ms.openlocfilehash: 1615e3a492cd91fb998680094f131db454945303
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182572"
---
# <a name="try-the-speech-service-for-free"></a>Speech Service 체험해 보기

Speech Service는 쉽고 저렴하게 시작할 수 있습니다. 30일 평가판을 통해 서비스 기능을 확인하고 서비스가 응용 프로그램 요구 사항에 적합한지 판단할 수 있습니다.

시간이 더 필요하면 Microsoft Azure 계정에 등록하세요. 그러면 최대 30일 동안 유료 Speech Service에 적용할 수 있는 $200의 서비스 크레딧이 제공됩니다.

마지막으로 Speech Service는 응용 프로그램 개발에 적합한 작은 용량의 체험 계층을 제공합니다. 서비스 크레딧이 만료된 후에도 이 무료 구독을 유지할 수 있습니다.

## <a name="free-trial"></a>평가판

30일 평가판을 사용하면 제한된 기간 동안 표준 가격 책정 계층에 액세스할 수 있습니다.

30일 평가판에 등록하려면 다음을 수행합니다.

1. [Cognitive Services 체험하기](https://azure.microsoft.com/try/cognitive-services/)로 이동합니다.

1. **Speech API** 탭을 선택합니다.

   ![Speech Services 탭](media/index/try-speech-api-free-trial1.png)
   
1. **Speech Services** 아래에서 **API 키 가져오기** 단추를 선택합니다.

   ![API 키](media/index/try-speech-api-free-trial2.png)

1. 사용 약관에 동의하고 드롭다운 메뉴에서 로캘을 선택합니다.

   ![사용 약관에 동의](media/index/try-speech-api-free-trial3.png)

1. Microsoft, Facebook, LinkedIn 또는 GitHub 계정을 사용하여 로그인합니다.

    [Microsoft 계정 포털](https://account.microsoft.com/account)에서 체험 Microsoft 계정을 등록할 수 있습니다. 시작하려면 **Microsoft에 로그인**을 클릭한 다음, 로그인하라는 메시지가 표시되면 **계정 만들기**를 클릭합니다. 단계에 따라 새 Microsoft 계정을 만들고 확인합니다.

[Cognitive Services 체험하기]에 로그인하면 체험 평가판이 시작됩니다. 표시되는 웹 페이지에는 현재 평가판 구독이 있는 모든 Azure Cognitive Services가 나열됩니다. 두 개의 구독 키가 **Speech Services** 옆에 나열됩니다. 응용 프로그램에서 두 키 중 하나를 사용할 수 있습니다.

> [!NOTE]
> 미국 서부 지역에서는 모든 평가판 구독을 사용할 수 있습니다. 요청 시 `westus` 엔드포인트를 사용해야 합니다.

## <a name="new-azure-account"></a>새 Azure 계정

새 Azure 계정에는 최대 30일 동안 사용할 수 있는 200달러 상당의 서비스 크레딧이 제공됩니다. 이 크레딧을 사용하여 Speech 서비스를 더 자세히 살펴보거나 응용 프로그램 개발을 시작할 수 있습니다.

새 Azure 계정을 등록하려면 [Azure 등록 페이지](https://azure.microsoft.com/free/ai/)로 이동하여 **체험 시작하기**를 클릭하고, Microsoft 계정을 사용하여 새 Azure 계정을 만듭니다.

[Microsoft 계정 포털](https://account.microsoft.com/account)에서 체험 Microsoft 계정을 등록할 수 있습니다. 시작하려면 **Microsoft에 로그인**을 클릭한 다음, 로그인하라는 메시지가 표시되면 **계정 만들기**를 클릭합니다. 단계에 따라 새 Microsoft 계정을 만들고 확인합니다.

Azure 계정이 만들어지면 다음 섹션의 단계에 따라 Speech 서비스에 대한 구독을 시작합니다.

## <a name="create-a-speech-resource-in-azure"></a>Azure에서 음성 리소스 만들기

Speech 서비스 리소스(체험 또는 유료 계층)를 Azure 계정에 추가하려면 다음을 수행합니다.

1. Microsoft 계정을 사용하여 [Azure Portal](https://ms.portal.azure.com/)에 로그인합니다.

1. 포털의 왼쪽 위에서 **리소스 만들기**를 선택합니다.

    ![리소스 만들기](media/index/try-speech-api-create-speech1.png)

1. **새로 만들기** 창에서 **speech**를 검색합니다.

1. 검색 결과에서 **Speech**를 선택합니다.

    ![Speech 선택](media/index/try-speech-api-create-speech2.png)

1. **Speech** 아래에서 **만들기** 단추를 선택합니다.

    ![만들기 단추 선택](media/index/try-speech-api-create-speech3.png)

1. **만들기** 아래에서 다음을 입력합니다.

    * 새 리소스 그룹의 이름. 이름은 동일한 서비스에 대한 여러 구독을 구분하는 데 도움이 됩니다.
    * 새 리소스가 연결되는 Azure 구독을 선택하여 요금이 얼마나 청구되는지 확인합니다.
    * 리소스가 사용될 지역을 선택합니다. 현재 Speech Service는 동아시아, 북유럽 및 미국 서부 지역에서 사용할 수 있습니다.
    * 체험 또는 유료 가격 책정 계층을 선택합니다. 각 계층의 가격 및 사용 할당량에 대한 전체 정보를 보려면 **전체 가격 책정 세부 정보 보기**를 클릭합니다.
    * 이 Speech 구독에 대한 새 리소스 그룹을 만들거나 기존 리소스 그룹에 해당 구독을 할당합니다. 리소스 그룹은 다양한 Azure 구독의 구성을 유지하는 데 도움이 됩니다.
    * 나중에 구독에 편리하게 액세스하려면 **대시보드에 고정** 확인란을 선택합니다.
    * **만들기**를 선택합니다.

    ![만들기 단추 선택](media/index/try-speech-api-create-speech4.png)

    새 Speech 리소스를 만들고 배포하는 데 약간의 시간이 걸립니다. 새 리소스에 대한 정보를 보려면 **빠른 시작**을 선택합니다.

    ![빠른 시작 패널](media/index/try-speech-api-create-speech5.png)

1. **빠른 시작** 아래의 1단계에서 **키** 링크를 클릭하여 구독 키를 표시합니다. 각 구독에는 두 개의 키가 있으며, 응용 프로그램에서 두 키 중 하나를 사용할 수 있습니다. 코드에 붙여넣기 위해 클립보드에 복사하려면 각 키 옆에 있는 단추를 선택합니다.

> [!NOTE]
> 표준 계층 구독은 하나 이상의 지역에서 무제한으로 만들 수 있지만, 체험 계층 구독은 하나만 만들 수 있습니다. 7일 동안 사용하지 않은 상태로 유지되는 체험 계층에 대한 모델 배포는 자동으로 해제됩니다.

## <a name="switch-to-a-new-subscription"></a>새 구독으로 전환

평가판이 만료되거나 응용 프로그램을 게시할 때와 같이 한 구독에서 다른 구독으로 전환하려면 코드의 지역과 구독 키를 새 Azure 리소스의 지역과 구독 키로 바꿉니다.

> [!NOTE]
> 평가판 키는 미국 서부(`westus`) 지역에서 만들어집니다. Azure 대시보드를 통해 만든 구독을 선택하면 다른 지역에 있을 수 있습니다.

* 응용 프로그램에서 [Speech SDK](speech-sdk.md)를 사용하는 경우 Speech 구성을 만들 때 `westus`와 같은 지역 코드를 제공합니다.
* 응용 프로그램에서 Speech 서비스의 [REST API](rest-apis.md) 중 하나를 사용하는 경우 지역은 요청 시 사용하는 엔드포인트 URI의 일부가 됩니다.

지역에 대해 만든 키는 해당 지역에서만 유효합니다. 다른 지역에서 사용하려고 하면 인증 오류가 발생합니다.

## <a name="next-steps"></a>다음 단계

10분 빠른 시작 중 하나를 수행하거나 SDK 샘플을 확인합니다.

> [!div class="nextstepaction"]
> [빠른 시작: C#에서 음성 인식](quickstart-csharp-dotnet-windows.md)
> [Speech SDK 샘플](speech-sdk.md#get-the-samples)
