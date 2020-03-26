---
title: Portal을 사용하여 Internet Analyzer 테스트 만들기 | Microsoft Docs
description: 이 문서에서는 첫 Internet Analyzer 테스트를 만드는 방법을 알아봅니다.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "73509837"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Portal(미리 보기)을 사용하여 인터넷 분석기 테스트 만들기

Internet Analyzer 리소스는 두 가지 방식, 즉 Azure Portal 또는 [CLI](internet-analyzer-cli.md)를 사용하여 만들 수 있습니다. 이 섹션을 참조하면 Portal 환경을 사용하여 새 Azure Internet Analyzer 리소스를 쉽게 만들 수 있습니다.

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

## <a name="before-you-begin"></a>시작하기 전에

공개 미리 보기는 전 세계에서 사용할 수 있습니다. 하지만 데이터 스토리지는 미리 보기 기간 동안 *미국 서부 2*로 제한됩니다.

## <a name="basics"></a>기본 사항

1. [Azure Internet Analyzer FAQ](internet-analyzer-faq.md)의 **미리 보기에 참여하는 방법** 지침에 따라 Internet Analyzer 미리 보기에 대한 액세스 권한을 얻으세요.
2. [Azure Portal](https://preview.portal.azure.com)의 홈페이지에서 **+ 리소스 만들기**를 클릭합니다. 현재 Internet Analyzer는 Azure Portal 미리 보기 버전에서만 사용할 수 있습니다.
3. **새로 만들기** 페이지의 *Marketplace 검색* 필드에서 "Internet Analyzer"를 검색합니다.
4. **Internet Analyzer(미리 보기)** 를 클릭합니다. 게시자가 *Microsoft*이고 범주가 *네트워킹*인지 확인합니다.
5. **Internet Analyzer(미리 보기)** 페이지에서 **만들기**를 클릭하여 **Internet Analyzer 만들기** 페이지를 엽니다.
6. Internet Analyzer 리소스에 대한 다음과 같은 구성 설정을 지정합니다.

    * **구독:** 새 Internet Analyzer 리소스를 호스팅하는 Azure 구독입니다. ***미리 보기 액세스를 요청하는 데 사용된 것과 동일한 구독 ID를 사용하세요.***
    * **리소스 그룹:** 새 Internet Analyzer 리소스가 만들어질 Azure 리소스 그룹입니다. 기존 리소스 그룹이 없는 경우 새 리소스 그룹을 만들 수 있습니다.
    * **이름:** 새 Internet Analyzer 리소스 프로필의 이름입니다.
    * **지역:** 리소스가 만들어질 Azure 공용 지역입니다. 미리 보기 중에는 *미국 서부 2*만 사용할 수 있습니다.

7. 프로필 설정 지정을 마쳤으면, **검토 + 만들기**를 클릭합니다.

## <a name="configuration"></a>구성

기본 단계를 완료하는 것은 테스트를 구성하고 JavaScript 클라이언트를 포함하기 위한 필수 조건입니다. 프로필을 만들었으면 **설정 > 구성**으로 이동하여 첫 테스트를 구성합니다.

1. **테스트 이름** 상자에 테스트 이름을 지정합니다.
2. **설명** 필드에 테스트에 대한 설명을 추가합니다.
3. **엔드포인트 구성**을 클릭하면 오른쪽에서 탭이 표시됩니다. 구성하려는 엔드포인트의 유형(단일 Azure 지역, 여러 Azure 지역 또는 사용자 지정 엔드포인트)을 선택합니다.

    >
    ***미리 구성된 엔드포인트: 단일 및 여러 Azure 지역 조합***
    * [미리 구성된 Azure 엔드포인트 목록](internet-analyzer-faq.md)에서 단일 지역 또는 지역 집합을 선택합니다.
    * 그런 다음, 평가할 애플리케이션 또는 콘텐츠 전송 아키텍처의 유형을 선택합니다.
        * 단일 Azure 지역: 사이트 가속([Azure Front Door](https://azure.microsoft.com/services/frontdoor/)), 정적 콘텐츠 캐싱([Azure CDN for Microsoft](https://azure.microsoft.com/services/cdn/)) 또는 없음입니다.
        * 여러 Azure 지역: 사이트 가속([Azure Front Door](https://azure.microsoft.com/services/frontdoor/)), DNS 제어([Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/))  

    ***사용자 지정 엔드포인트***
    * [사용자 지정 엔드포인트 만들기](internet-analyzer-custom-endpoint.md) 페이지의 지침을 따릅니다.
    * 포털에 1픽셀 이미지의 HTTPS URL 위치를 붙여넣습니다.
    >

4. **추가**를 클릭하여 테스트에 엔드포인트를 추가합니다.
5. 1~4단계를 반복하여 두 번째 엔드포인트를 구성합니다. 엔드포인트 B는 항상 엔드포인트 A에 상대적으로 측정됩니다. 엔드포인트를 구성할 때 어떤 엔드포인트를 테스트 컨트롤로 사용할지 고려해야 합니다.
6. **저장** 단추를 클릭하여 테스트를 저장합니다. 테스트를 저장한 후에는 더 이상 특정 테스트의 엔드포인트를 편집할 수 없습니다.
7. 시작하려는 테스트를 선택하고 **테스트 시작**을 클릭합니다. 그러면 테스트의 ***상태***가 ***실행 중***으로 변경됩니다. 언제든지 테스트를 시작할 수 있지만 테스트가 측정값을 수집하려면 JavaScript 클라이언트가 포함되어 있어야 합니다.
8. 아무 때나 테스트를 더 추가합니다. 테스트 하나를 만들기 전에는 고유한 JavaScript 클라이언트가 생성되지 않습니다.

## <a name="embed-client"></a>클라이언트 포함

테스트를 시작하려면 웹 애플리케이션에 JavaScript 클라이언트를 포함시켜야 합니다. 하나 이상의 테스트를 구성한 후, **검토 + 만들기**를 클릭하고 **설정 > 구성**으로 이동하여 JavaScript 클라이언트를 복사합니다. 구체적인 지침은 [Internet Analyzer 클라이언트 포함](internet-analyzer-embed-client.md) 페이지에서 찾을 수 있습니다.  

## <a name="next-steps"></a>다음 단계

* [Internet Analyzer FAQ](internet-analyzer-faq.md) 읽기
* [Internet Analyzer 클라이언트](internet-analyzer-embed-client.md)를 포함하고 [사용자 지정 엔드포인트](internet-analyzer-custom-endpoint.md)를 만드는 방법에 대해 자세히 알아보세요.
