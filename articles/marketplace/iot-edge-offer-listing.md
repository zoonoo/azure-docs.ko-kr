---
title: Azure Marketplace에서 IoT Edge 모듈 제품 목록 세부 정보 구성
description: Azure Marketplace에서 IoT Edge 모듈 제품 목록 세부 정보를 구성합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: 285b57d5d4094f51611b36794584a83d3d115b66
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111542087"
---
# <a name="configure-iot-edge-module-offer-listing-details"></a>IoT Edge 모듈 제품 목록 세부 정보 구성

이 페이지에서는 제안 이름, 설명, 링크, 연락처, 로고, 스크린샷 등 제안 세부 정보를 정의할 수 있습니다.

> [!NOTE]
> 제안 목록 세부 정보는 한 가지 언어로만 제공됩니다. 제안 설명이 “이 애플리케이션은 [영어가 아닌 언어]로만 제공됩니다.” 문구로 시작하는 경우 영어를 사용하지 않아도 됩니다. 제안 목록 콘텐츠에 사용된 언어가 아닌 다른 언어로 된 제안 콘텐츠에 대한 ‘유용한 링크 URL’을 제공할 수도 있습니다.

다음은 Azure Marketplace에서 제품 정보가 표시되는 방식에 대한 예입니다(표시된 모든 가격은 예를 들기 위한 것이며 실제 비용을 반영하지 않음).

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-offer.png" alt-text="이 제품이 Azure Marketplace에 어떻게 표시되는지 보여 줍니다.":::

##### <a name="call-out-descriptions"></a>설명

1. 로고
1. 범주
1. 지원 주소(링크)
1. 사용 약관
1. 개인정보처리방침(링크)
1. Offer name
1. 제품 요약
1. Description
1. 자세한 정보 링크
1. 스크린샷/비디오

<br>다음은 Azure Marketplace 검색 결과에 제품 정보가 어떻게 표시되는지 보여 주는 예입니다.

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-offer-search-results.png" alt-text="이 제품이 Azure Marketplace 검색 결과에 어떻게 표시되는지 보여 줍니다.":::

##### <a name="call-out-descriptions"></a>설명

1. 작은 로고
2. Offer name
3. 검색 결과 요약

<br>다음은 Azure Portal에 제품 정보가 어떻게 표시되는지 보여 주는 예입니다.

:::image type="content" source="media/iot-edge/example-iot-azure-portal-offer.png" alt-text="해당 제품이 Azure Portal에 어떻게 표시되는지 보여 줍니다.":::

##### <a name="call-out-descriptions"></a>설명

1. Name
2. Description
3. 유용한 링크
4. 스크린샷

<br>다음은 Azure Portal 검색 결과에 제품 정보가 어떻게 표시되는지 보여 주는 예입니다.

:::image type="content" source="media/iot-edge/example-iot-azure-portal-offer-search-results.png" alt-text="이 제품이 Azure Portal 검색 결과에 어떻게 표시되는지 보여 줍니다.":::

##### <a name="call-out-descriptions"></a>설명

1. 작은 로고
2. Offer name
3. 검색 결과 요약

## <a name="marketplace-details"></a>마켓플레이스 세부 정보

여기에 입력한 **이름** 은 고객에게 제안 제목으로 표시됩니다. 이 필드는 제안을 만들 때 **제안 별칭** 에 입력한 이름으로 미리 채워져 있지만 변경할 수 있습니다. 이름:

- 상표 및 저작권 기호를 포함할 수 있습니다.
- 50자 이하여야 합니다.
- 이모지를 포함할 수 없습니다.

**검색 결과 요약** 에 간단한 제안 설명을 입력합니다(최대 100자). 이 설명은 Marketplace 검색 결과에서 사용할 수 있습니다.

제안과 관련한 **간단한 설명** 을 256자 이하로 입력합니다. 이 설명은 검색 결과 및 제안의 세부 정보 페이지에 표시됩니다.

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

HTML 태그를 사용하여 설명의 서식을 지정하면 더 흡입력 있는 설명을 만들 수 있습니다. 허용되는 태그 목록은 [지원되는 HTML 태그](supported-html-tags.md)를 참조하세요.

조직의 개인 정보 취급 방침에 대한 **개인 정보 취급 방침 링크**(URL)를 입력합니다. 앱이 개인 정보 보호 법률 및 규정을 준수하는지 확인하고 유효한 개인정보처리방침을 제공하는 것은 게시자의 책임입니다.

## <a name="useful-links"></a>유용한 링크

제품에 대한 추가 온라인 문서를 제공합니다. 최대 25개의 링크를 추가할 수 있습니다. 링크를 추가하려면 **+ 링크 추가** 를 선택하고 다음 필드를 완료합니다.

- **제목** - 제품의 세부 정보 페이지에 제목이 고객에게 표시됩니다.
- **링크(URL)** - 고객이 온라인 설명서를 볼 수 있는 링크를 입력합니다. 링크는 `http://` 또는 `https://`로 시작해야 합니다.

 [Azure IoT 디바이스 카탈로그](https://catalog.azureiotsolutions.com/)에서 설명서에 하나 이상의 링크 및 호환 IoT Edge 디바이스에 하나의 링크를 추가합니다.

### <a name="contact-information"></a>연락처 정보

**지원 연락처**, **엔지니어링 연락처**, **클라우드 솔루션 공급자 프로그램** 연락처의 이름, 메일, 전화번호를 제공합니다. 이 정보는 고객에게 표시되지 않지만 Microsoft에서 사용할 수 있으며 CSP 파트너에게 제공될 수 있습니다.

**클라우드 솔루션 제공업체 프로그램 연락처** 섹션에서는 Azure 글로벌, Azure Government 또는 둘 다에서 제품이 제공되는지 여부에 따라 파트너가 제품에 대한 지원을 찾을 수 있는 **Azure 글로벌 고객을 위한 지원 웹사이트** 주소를 제공합니다.

**지원 연락처** 섹션에서는 CSP 파트너가 제품에 대한 마케팅 자료와 지원을 찾을 수 있는 링크(**CSP 프로그램 마케팅 자료**) 주소를 제공합니다.

## <a name="marketplace-media"></a>마켓플레이스 미디어

제품에 사용할 로고 및 이미지를 제공합니다. 모든 이미지는 PNG 형식이어야 합니다. 이미지가 흐리면 제출이 거부됩니다.

>[!NOTE]
>파일을 업로드하는 데 문제가 있으면 파트너 센터에서 사용하는 https://upload.xboxlive.com 서비스가 로컬 네트워크에서 차단되지 않는지 확인합니다.

### <a name="logos"></a>로고

**큰** 크기 로고의 경우 PNG 파일을 제공합니다. 파트너 센터는 이를 사용하여 다른 필요한 크기를 만듭니다. 필요에 따라 나중에 이를 다른 이미지로 바꿀 수 있습니다.

이러한 로고는 목록의 여러 위치에서 사용됩니다.

[!INCLUDE [logos-appsource-only](includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>스크린샷

제품의 작동 방식을 보여 주는 최대 5개의 스크린샷(선택 사항)을 추가합니다. 스크린샷은 1280x720픽셀이고 PNG 형식이어야 합니다. 각 스크린샷에 대한 캡션을 추가합니다.

### <a name="videos"></a>동영상

필요에 따라 제안에 대해 설명하는 동영상을 최대 5개까지 추가합니다. 동영상은 외부 동영상 서비스에서 호스트되어야 합니다. 각 동영상의 이름, 웹 주소 및 동영상의 썸네일 PNG 이미지(1280 x 720 픽셀)를 입력합니다.

리소스를 나열하는 추가 마켓플레이스는 [마켓플레이스 제품 목록에 대한 모범 사례](gtm-offer-listing-best-practices.md)를 참조하세요.

왼쪽 탐색 메뉴 **미리 보기 대상 그룹** 에서 다음 탭을 계속 진행하려면 **초안 저장** 을 선택하세요.

## <a name="next-steps"></a>다음 단계

- [미리 보기 대상 그룹 설정](iot-edge-preview-audience.md)
