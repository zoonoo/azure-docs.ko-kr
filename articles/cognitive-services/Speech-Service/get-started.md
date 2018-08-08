---
title: Speech Service 체험해 보기
description: Speech Service를 체험해 보는 방법을 알아봅니다.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/17/2018
ms.author: v-jerkin
ms.openlocfilehash: ff9a258d42f25ded82545909cdeade119548148e
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325210"
---
# <a name="try-the-speech-service-for-free"></a>Speech Service 체험해 보기

Speech Service는 쉽고 저렴하게 시작할 수 있습니다. 30일 평가판을 통해 서비스 기능을 확인하고 서비스가 응용 프로그램 요구 사항에 적합한지 판단할 수 있습니다.

시간이 더 필요하면 Microsoft Azure 계정에 등록하세요. 그러면 최대 30일 동안 유료 Speech Service에 적용할 수 있는 $200의 서비스 크레딧이 제공됩니다.

마지막으로 Speech Service는 응용 프로그램 개발에 적합한 볼륨이 적은 무료 계층을 제공합니다. 서비스 크레딧이 만료된 후에도 이 무료 구독을 유지할 수 있습니다.

## <a name="free-trial"></a>평가판

30일 평가판을 통해 제한된 시간 동안 S0 표준 가격 책정 계층에 액세스할 수 있습니다. 30일 평가판에 등록하려면 다음 단계를 따르세요.

1. [Cognitive Services 체험해 보기](https://azure.microsoft.com/try/cognitive-services/) 페이지로 이동합니다.

1. [음성] 탭으로 전환하고 “Speech Services” 옆에 있는 **API 키 가져오기** 단추를 클릭합니다.

   ![Speech Services 탭](media/index/try-speech-api-free-trial1.png)<br>
   ![API 키](media/index/try-speech-api-free-trial2.png)

3. 사용 약관에 동의하고 드롭다운 메뉴에서 로캘을 선택합니다.

   ![사용 약관에 동의](media/index/try-speech-api-free-trial3.png)

4. Microsoft, Facebook, LinkedIn 또는 GitHub 계정을 사용하여 로그인합니다. 또는 체험 Microsoft 계정에 등록할 수 있습니다.

    * [Microsoft 계정 포털](https://account.microsoft.com/account)로 이동합니다.
    * **Microsoft에 로그인**을 클릭합니다.

    ![로그인](media/index/try-speech-api-free-trial4.png)

    * 로그인할지 묻는 메시지가 나타나면 “계정 만들기”를 클릭합니다.

    ![새 계정 만들기](media/index/try-speech-api-free-trial5.png)

    * 다음 단계에서 메일 주소 또는 전화 번호를 입력하고, 암호를 할당하고, 지침에 따라 새 Microsoft 계정을 확인합니다.

로그인한 후 평가판이 시작됩니다. 표시된 웹 페이지에는 현재 평가판 구독을 사용 중인 모든 Cognitive Services가 나열됩니다. 두 개의 구독 키가 “Speech Services” 옆에 나열됩니다. 응용 프로그램에서 두 키 중 하나를 사용할 수 있습니다.

> [!NOTE]
> 미국 서부 지역에서는 모든 평가판 구독을 사용할 수 있습니다. 요청을 만들 때 사용자 지역에 해당하는 끝점을 사용해야 합니다.

## <a name="new-azure-account"></a>새 Azure 계정

새 Azure 계정은 최대 30일 동안 지속되는 $200의 서비스 크레딧을 받습니다. 이 크레딧은 Speech Service를 추가 탐색하거나 응용 프로그램 개발을 시작하는 데 사용할 수 있습니다.

새 Azure 계정에 등록하려면 다음 단계를 따르세요.

1. [Azure 등록 페이지](https://azure.microsoft.com/free/ai/)로 이동합니다. 

1. **무료로 시작**을 클릭합니다.

    ![체험하기](media/index/try-speech-api-new-azure1.png)

3. Microsoft 계정으로 로그인합니다. 계정이 없는 경우:

    * [Microsoft 계정 포털](https://account.microsoft.com/account)로 이동합니다.
    * **Microsoft에 로그인**을 클릭합니다.
    * 로그인할지 묻는 메시지가 나타나면 “계정 만들기”를 클릭합니다.
    * 다음 단계에서 메일 주소 또는 전화 번호를 입력하고, 암호를 할당하고, 지침에 따라 새 Microsoft 계정을 확인합니다.

1. 계정에 등록하는 데 필요한 나머지 정보를 입력합니다. 국가 및 이름을 지정하고 전화 번호와 메일 주소를 제공합니다.

    ![정보 입력](media/index/try-speech-api-new-azure2.png)

    신용 카드 번호를 입력하거나 전화를 통해 신원을 확인한 다음, Azure 사용자 계약에 동의합니다. (신용 카드에 요금이 청구되지 않습니다.)

    ![계약에 동의](media/index/try-speech-api-new-azure3.png)

체험 Azure 계정이 생성됩니다. 다음 섹션의 단계에 따라 Speech Service에 대한 구독을 시작합니다.

## <a name="create-a-speech-resource-in-azure"></a>Azure에서 음성 리소스 만들기

Azure 계정에 Speech Service 리소스를 추가하려면 다음 단계를 따르세요.

1. Microsoft 계정을 사용하여 [Azure Portal](https://ms.portal.azure.com/)에 로그인합니다.

1. 포털의 왼쪽 위에서 **리소스 만들기**(녹색 **+** 아이콘)를 클릭합니다.

    ![리소스 만들기](media/index/try-speech-api-create-speech1.png)

1. [새로 만들기] 창에서 Speech를 검색합니다.

    ![Speech 클릭](media/index/try-speech-api-create-speech2.png)

1. 검색 결과에서 “Speech(미리 보기)”를 클릭합니다.

1. Speech Service 패널 아래쪽에서 **만들기** 단추를 클릭합니다.

    ![만들기 클릭](media/index/try-speech-api-create-speech3.png)

1. 만들기 패널에서 다음을 입력합니다.

    * 새 리소스 그룹의 이름. 이름은 동일한 서비스에 대한 여러 구독을 구분하는 데 도움이 됩니다.
    * 새 리소스가 연결되는 Azure 구독을 선택하여 요금이 얼마나 청구되는지 확인합니다.
    * 리소스가 사용될 지역을 선택합니다. 현재 Speech Service는 동아시아, 북유럽 및 미국 서부 지역에서 사용할 수 있습니다.
    * 가격 책정 계층, F0(무제한 무료 구독) 또는 S0(표준 구독)을 선택합니다. 각 계층의 가격 및 사용 할당량에 대한 전체 정보를 보려면 **전체 가격 책정 세부 정보 보기**를 클릭합니다.
    * 이 Speech 구독에 대한 새 리소스 그룹을 만들거나 이 구독을 기존 리소스 그룹에 할당합니다. 리소스 그룹은 다양한 Azure 구독의 구성을 유지하는 데 도움이 됩니다.
    * 나중에 구독에 편리하게 액세스하려면 **대시보드에 고정** 확인란을 선택합니다.
    * **만들기**를 클릭합니다.

    ![패널에서 만들기 클릭](media/index/try-speech-api-create-speech4.png)

    새 Speech 리소스를 만들고 배포하는 데 잠시 시간이 걸릴 수 있습니다. 새 리소스에 대한 정보가 포함된 빠른 시작 패널이 나타납니다.

    ![빠른 시작 패널](media/index/try-speech-api-create-speech5.png)

1. 빠른 시작 패널의 1단계에서 **키** 링크를 클릭하여 구독 키를 표시합니다. 각 구독에는 두 개의 키가 있고 응용 프로그램에서 하나를 사용할 수 있습니다. 각 키 옆에 있는 단추를 클릭하여 코드에 붙여넣을 수 있도록 클립보드에 복사합니다.

> [!NOTE]
> 하나 이상의 지역에서 무제한으로 표준 계층 구독을 만들 수 있습니다. 그러나 하나의 무료 계층 구독은 하나만 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

10분 빠른 시작 중 하나를 수행하거나 SDK 샘플을 확인합니다.

> [!div class="nextstepaction"]
> [빠른 시작: C#에서 음성 인식](quickstart-csharp-dotnet-windows.md)
> [음성 SDK 샘플](speech-sdk.md#get-the-samples)
