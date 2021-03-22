---
author: baanders
description: Visual Studio의 Azure 함수를 게시하는 프로세스를 위한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 9f8ca9ab6dc184abb8d9dcd25ebe87cbd7761722
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102630282"
---
프로젝트를 Azure의 함수 앱에 게시하려면 *솔루션 탐색기* 에서 프로젝트를 마우스 오른쪽 단추로 선택하고 **게시** 를 선택합니다.

> [!IMPORTANT] 
> Azure의 함수 앱에 게시하면 Azure Digital Twins와 관계없이 구독에 추가 요금이 발생합니다.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="오른쪽 선택 솔루션 메뉴를 보여주는 Visual Studio의 스크린샷. 메뉴에 게시가 강조 표시됩니다.":::

다음에 나오는 *게시* 페이지에서 기본 대상 선택 항목인 **Azure** 를 그대로 두고 *다음* 을 선택합니다. 

특정 대상에 대해 **Azure 함수 앱(Windows)** 을 선택하고 *다음* 을 선택합니다.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Azure 함수 게시 대화 상자에서 Visual Studio의 스크린샷. 특정 대상 페이지에서 Azure 함수 앱(Windows)이 선택되었습니다.":::

*Functions 인스턴스* 페이지에서 구독을 선택합니다. 그런 다음, *+* 아이콘을 선택하여 새 Azure 함수를 만듭니다.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Azure 함수 게시 대화 상자에서 Visual Studio의 스크린샷. 새 함수를 만드는 + 단추가 함수 인스턴스 페이지에 강조 표시됩니다.":::

*함수 앱(Windows) - 새 만들기* 창에서 다음과 같이 필드를 입력합니다.
* **이름** 은 Azure에서 Azure Functions 앱을 호스트하는 데 사용하는 소비 계획의 이름입니다. 이는 실제 함수를 보유하는 함수 앱의 이름도 됩니다. 고유 값을 선택하거나 기본 제안을 그대로 둘 수 있습니다.
* **구독** 이 사용하려는 구독과 일치하는지 확인합니다. 
* **리소스 그룹** 이 사용하려는 리소스 그룹과 일치하는지 확인합니다.
* **계획 유형** 을 *소비* 로 둡니다.
* 리소스 그룹의 위치와 일치하는 **위치** 를 선택합니다.
* *새로 만들기...* 링크를 사용하여 새 **Azure Storage** 리소스를 만듭니다. 리소스 그룹에 맞게 위치를 설정하고, 다른 기본값을 사용하고, "확인"을 선택합니다.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Azure 함수 게시 대화 상자에서 Visual Studio의 스크린샷. 이름, 구독, 리소스 그룹, 계획 유형, 위치 및 Azure Storage를 포함하여 새 함수 앱의 세부 정보가 채워집니다.":::

그런 다음 **만들기** 를 선택합니다.

앱 서비스를 만드는 동안 잠시 기다린 후 대화 상자가 *Functions 인스턴스* 페이지로 돌아가 새 함수 앱이 리소스 그룹 아래 중첩된 **함수 앱** 영역에 표시됩니다. *마침* 을 선택합니다.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Visual Studio에서 Azure 함수 게시: Functions 인스턴스(함수 앱 후)":::

주 Visual Studio 창에서 다시 열리는 *게시* 창에서 모든 정보가 올바른지 확인하고 **게시** 를 선택합니다.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Azure 함수 게시 대화 상자에서 Visual Studio의 스크린샷. 새로운 함수 앱이 함수 앱 목록에 나타나고 완료 단추가 있습니다.":::

> [!NOTE]
> 다음과 같은 팝업이 표시되는 경우: :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="자격 증명 게시라는 Visual Studio 팝업 창의 스크린샷. 여기에는 사용자 이름 및 암호 필드와 Azure에서 자격 증명 검색 시도 단추가 포함되어 있습니다." border="false":::
> **Azure에서 자격 증명 검색 시도** 와 **저장** 을 선택합니다.
>
> *Azure에서 Functions 버전 업그레이드* 또는 *함수 런타임 버전이 Azure에서 실행되는 버전과 일치하지 않음* 경고가 표시되는 경우:
>
> 프롬프트에 따라 최신 Azure Functions 런타임 버전으로 업그레이드합니다. 이전 버전의 Visual Studio를 사용하는 경우 이 문제가 발생할 수 있습니다.

이제 함수 앱이 Azure에 게시됩니다.