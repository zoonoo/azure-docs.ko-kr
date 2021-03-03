---
title: Translator 리소스 만들기
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive Services Translator 리소스를 만들고 구독 키 및 끝점 URL을 가져오는 방법을 보여 줍니다.
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: a0d8532d19aff41bc5e7defb3b58462e81018749
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712932"
---
# <a name="create-a-translator-resource"></a>Translator 리소스 만들기

이 문서에서는 Azure Portal에서 변환기 리소스를 만드는 방법에 대해 알아봅니다. [Azure Translator](translator-info-overview.md) 는 [Azure Cognitive Services](../what-are-cognitive-services.md) REST api 제품군의 일부인 클라우드 기반 기계 번역 서비스입니다. Azure 리소스는 사용자가 만드는 서비스의 인스턴스입니다. Azure 서비스에 대 한 모든 API 요청에는 액세스 인증을 위한 **끝점** URL 및 읽기 전용 **구독 키** 가 필요 합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작 하려면 활성 [**Azure 계정이**](https://azure.microsoft.com/free/cognitive-services/)필요 합니다.  없는 경우 [**12 개월 무료 구독을 만들**](https://azure.microsoft.com/free/)수 있습니다.

## <a name="translator-resource-types"></a>Translator 리소스 유형

Translator 서비스는 다음 두 가지 리소스 유형으로 액세스할 수 있습니다.

* 단일 **서비스** 리소스 종류를 사용 하 여 단일 서비스 API 키 및 끝점에 액세스할 수 있습니다.  

* **다중 서비스** 리소스 유형을 사용 하면 단일 API 키 및 끝점을 사용 하 여 여러 Cognitive Services에 액세스할 수 있습니다. 현재 Cognitive Services 리소스는 다음 서비스에 사용할 수 있습니다.
  * Language ([Translator](../translator/translator-info-overview.md), [Language Understanding (LUIS)](../luis/what-is-luis.md), [Text Analytics](../text-analytics/overview.md))  
  * 비전 ([Computer Vision](../computer-vision/overview.md)), ([얼굴](../face/overview.md))  
  * 결정 ([Content Moderator](../content-moderator/overview.md))  

## <a name="create-your-resource"></a>리소스 만들기

* Azure Portal의 [**번역기 만들기**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) 페이지로 직접 이동 하 여 프로젝트 세부 정보를 완료 합니다.

* Azure Portal에서 [**Cognitive Services 만들기**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) 페이지로 직접 이동 하 여 프로젝트 세부 정보를 완료 합니다.

>[!TIP]
>원한다 면 Azure Portal 홈페이지에서 시작 하 여 다음과 같이 **만들기** 프로세스를 시작할 수 있습니다.
>
> 1. [**Azure 포털**](https://ms.portal.azure.com/#home) 홈 페이지로 이동 합니다.
> 1. Azure 서비스 메뉴에서 **리소스 만들기**  ➕ 선택 합니다.
>1. **Marketplace** 검색 검색 상자에서 **변환기** (단일 서비스 리소스) 또는 **Cognitive Services** (다중 서비스 리소스)를 입력 하 고 선택 합니다.  위의 [리소스 유형 선택](#create-your-resource)을 *참조* 하세요.
> 1. **만들기** 를 선택 하면 프로젝트 세부 정보 페이지로 이동 합니다.
><br/><br/>

## <a name="complete-your-project-and-instance-details"></a>프로젝트 및 인스턴스 세부 정보를 완료 합니다.

1. **구독**. 사용 가능한 Azure 구독 중 하나를 선택합니다.

1. **리소스 그룹**. 선택한 Azure 리소스 그룹은 새 리소스에 대 한 가상 컨테이너 역할을 합니다. 새 리소스 그룹을 만들거나 동일한 수명 주기, 사용 권한 및 정책을 공유 하는 기존 리소스 그룹에 리소스를 추가할 수 있습니다.

1. **리소스 영역**. 비즈니스 또는 응용 프로그램에 특정 지역이 필요 하지 않는 한 **Global** 을 선택 합니다. Translator는 비 지역별 서비스 이므로 특정 Azure 지역에 대 한 종속성이 없습니다.  [Azure의 지역 및 가용성 영역](../../availability-zones/az-overview.md)을 참조 하세요.

1. **이름**. 리소스에 대해 선택한 이름을 입력 합니다. 선택한 이름은 Azure 내에서 고유 해야 합니다.

> [!NOTE]
> 사용자 지정 도메인 끝점이 필요한 변환기 기능을 사용 하는 경우에는 이름 필드에 입력 한 값이 끝점에 대 한 사용자 지정 도메인 이름 매개 변수가 됩니다.

5. **가격 책정 계층**. 요구 사항에 맞는 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/translator) 을 선택 합니다.

   * 각 구독에는 체험 계층이 있습니다.
   * 무료 계층은 유료 계획과 동일한 기능 및 기능을 제공 하며 만료 되지 않습니다.
   * 계정 당 무료 구독을 하나만 사용할 수 있습니다.</li></ul>

1. 다중 서비스 리소스를 만든 경우에는 확인란을 통해 추가 사용 정보를 확인 해야 합니다.

1. **검토 + 만들기** 를 선택합니다.

1. 서비스 약관을 검토 하 고 **만들기** 를 선택 하 여 리소스를 배포 합니다.

1. 리소스가 성공적으로 배포 되 면 **리소스로 이동** 을 선택 합니다.

### <a name="authentication-keys-and-endpoint-url"></a>인증 키 및 끝점 URL

모든 Cognitive Services API 요청에는 인증을 위한 끝점 URL 및 읽기 전용 키가 필요 합니다.

* **인증 키**. 키는 변환 서비스에 대 한 모든 요청에 전달 되는 고유한 문자열입니다. 쿼리 문자열 매개 변수를 통해 키를 전달 하거나 HTTP 요청 헤더에서 키를 지정할 수 있습니다.

* **끝점 URL** 입니다. 특정 Azure 지역이 필요 하지 않는 한 API 요청에서 전역 끝점을 사용 합니다.  [기본 url](reference/v3-0-reference.md#base-urls)을 참조 하세요. 전역 끝점 URL은 `api.cognitive.microsofttranslator.com` 입니다.

## <a name="get-your-authentication-keys-and-endpoint"></a>인증 키 및 끝점 가져오기

1. 새 리소스를 배포한 후 **리소스로 이동** 또는 리소스 페이지로 직접 이동을 선택 합니다.
1. 왼쪽 레일의 *리소스 관리* 에서 **키 및 끝점** 을 선택 합니다.
1. *Microsoft 메모장과* 같은 편리한 위치에 구독 키와 끝점 URL을 복사 하 여 붙여 넣습니다.

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="키 및 끝점을 가져옵니다.":::

## <a name="how-to-delete-a--resource-or-resource-group"></a>리소스 또는 리소스 그룹을 삭제 하는 방법

> [!Warning]
> 리소스 그룹을 삭제 하면 해당 그룹에 포함 된 리소스도 모두 삭제 됩니다.

Cognitive Services 또는 번역기 리소스를 제거 하려면 리소스를 **삭제** 하거나 **리소스 그룹을 삭제** 하면 됩니다.

리소스를 삭제 하려면:

1. Azure Portal에서 리소스 그룹으로 이동 합니다.
1. 옆에 있는 확인란을 선택 하 여 삭제할 리소스를 선택 합니다.
1. 오른쪽 가장자리 근처의 최상위 메뉴에서 **삭제** 를 선택 합니다.
1. **삭제 된 리소스** 대화 상자에 *예* 를 입력 합니다.
1. **삭제** 를 선택합니다.

리소스 그룹을 삭제하려면 다음을 수행합니다.

1. Azure Portal에서 리소스 그룹으로 이동 합니다.
1. 왼쪽 가장자리 근처의 최상위 메뉴 모음에서 **리소스 삭제 그룹** 을 선택 합니다.
1. 리소스 그룹 이름을 입력 하 고 **삭제** 를 선택 하 여 삭제 요청을 확인 합니다.

## <a name="how-to-get-started-with-translator"></a>번역기를 시작 하는 방법

이 빠른 시작에서는 REST Api와 함께 변환기 서비스를 사용 하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [번역기 시작](quickstart-translator.md)

## <a name="more-resources"></a>추가 리소스

* [Microsoft Translator 코드 샘플](https://github.com/MicrosoftTranslator)입니다.  다중 언어 변환기 코드 샘플은 GitHub에서 사용할 수 있습니다.
* [Microsoft Translator 지원 포럼](https://www.aka.ms/TranslatorForum)
* [Azure 시작(3분 동영상)](https://azure.microsoft.com/get-started/?b=16.24)