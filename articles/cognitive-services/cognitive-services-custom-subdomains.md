---
title: 사용자 지정 하위 도메인
titleSuffix: Azure Cognitive Services
description: 각 코그너티브 서비스 리소스에 대한 사용자 지정 하위 도메인 이름은 Azure 포털, Azure Cloud Shell 또는 Azure CLI를 통해 만들어집니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647689"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>코그너티브 서비스에 대한 사용자 지정 하위 도메인 이름

Azure Cognitive Services는 [Azure 포털,](https://portal.azure.com) [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)또는 Azure [CLI를](https://docs.microsoft.com/cli/azure/install-azure-cli)통해 생성된 각 리소스에 대해 사용자 지정 하위 도메인 이름을 사용합니다. 특정 Azure 지역의 모든 고객에게 공통적인 지역 끝점과 달리 사용자 지정 하위 도메인 이름은 리소스에 고유합니다. 인증을 위해 Azure Active Directory(Azure AD)와 같은 기능을 활성화하려면 사용자 지정 하위 도메인 이름이 필요합니다.

## <a name="how-does-this-impact-existing-resources"></a>이는 기존 리소스에 어떤 영향을 미합니까?

2019년 7월 1일 이전에 생성된 코그너티브 서비스 리소스는 관련 서비스에 지역 끝점을 사용합니다. 이러한 끝점은 기존 리소스와 새 리소스에서 작동합니다.

Azure AD와 같은 기능을 사용하도록 설정할 수 있도록 기존 리소스를 마이그레이션하여 사용자 지정 하위 도메인 이름을 활용하려면 다음 지침을 따르십시오.

1. Azure 포털에 로그인하고 사용자 지정 하위 도메인 이름을 추가하려는 인지 서비스 리소스를 찾습니다.
2. **개요** 블레이드에서 사용자 지정 **도메인 이름 생성을**찾아 선택합니다.
3. 그러면 리소스에 대한 고유한 사용자 지정 하위 도메인을 만드는 지침이 포함된 패널이 열립니다.
   > [!WARNING]
   > 사용자 지정 하위 도메인 이름을 만든 후에는 변경할 **수 없습니다.**

## <a name="do-i-need-to-update-my-existing-resources"></a>기존 리소스를 업데이트해야 하나요?

아니요. 지역 끝점은 새 및 기존 코그너티브 서비스에 대해 계속 작동하며 사용자 지정 하위 도메인 이름은 선택 사항입니다. 사용자 지정 하위 도메인 이름이 추가되더라도 지역 끝점은 리소스와 계속 함께 작동합니다.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>SDK에서 리소스에 대한 리전을 요청하면 어떻게 됩니까?

> [!WARNING]
> 음성 서비스는 현재 사용자 지정 하위 도메인을 지원하지 **않습니다.** 음성 서비스 및 관련 SDK를 사용할 때는 지역 끝점을 사용하십시오.

지역 별점 및 사용자 지정 하위 도메인 이름은 모두 지원되며 상호 교환하여 사용할 수 있습니다. 그러나 전체 끝점이 필요합니다.

지역 정보는 [Azure 포털의](https://portal.azure.com)리소스에 대한 **개요** 블레이드에서 사용할 수 있습니다. 전체 지역 끝점 목록은 [지역 끝점 목록이 있습니까?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>사용자 지정 하위 도메인 이름이 지역입니까?

예. 사용자 지정 하위 도메인 이름을 사용 해도 코그너티브 서비스 리소스의 지역적 측면은 변경되지 않습니다.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>사용자 지정 하위 도메인 이름에 대한 요구 사항은 무엇입니까?

사용자 지정 하위 도메인 이름은 리소스에 고유합니다. 이름에는 숫자 문자와 `-` 문자만 포함될 수 있습니다. 길이가 2에서 64자 사이여야 하며 `-`으로 끝날 수 없습니다.

## <a name="can-i-change-a-custom-domain-name"></a>사용자 지정 도메인 이름을 변경할 수 있나요?

아니요. 사용자 지정 하위 도메인 이름을 만들고 리소스와 연결한 후에는 변경할 수 없습니다.

## <a name="can-i-reuse-a-custom-domain-name"></a>사용자 지정 도메인 이름을 다시 사용할 수 있나요?

각 사용자 지정 하위 도메인 이름은 고유하므로 Cognitive Services 리소스에 할당한 사용자 지정 하위 도메인 이름을 다시 사용하려면 기존 리소스를 삭제해야 합니다. 리소스가 삭제된 후 사용자 지정 하위 도메인 이름을 다시 사용할 수 있습니다.

## <a name="is-there-a-list-of-regional-endpoints"></a>지역 엔드포인트 목록이 있습니까?

예. Azure 인지 서비스 리소스와 함께 사용할 수 있는 지역 끝점 목록입니다.

> [!NOTE]
> 번역기 텍스트 API 및 Bing 검색 API는 전역 끝점을 사용합니다.

| 엔드포인트 유형 | 지역 | 엔드포인트 |
|---------------|--------|----------|
| Public | 글로벌(번역기 텍스트 & 빙) | `https://api.cognitive.microsoft.com` |
| | 오스트레일리아 동부 | `https://australiaeast.api.cognitive.microsoft.com` |
| | 브라질 남부 | `https://brazilsouth.api.cognitive.microsoft.com` |
| | 캐나다 중부 | `https://canadacentral.api.cognitive.microsoft.com` |
| | 미국 중부 | `https://centralus.api.cognitive.microsoft.com` |
| | 동아시아 | `https://eastasia.api.cognitive.microsoft.com` |
| | 미국 동부 | `https://eastus.api.cognitive.microsoft.com` |
| | 미국 동부 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | 프랑스 중부 | `https://francecentral.api.cognitive.microsoft.com` |
| | 인도 중부 | `https://centralindia.api.cognitive.microsoft.com` |
| | 일본 동부 | `https://japaneast.api.cognitive.microsoft.com` |
| | 한국 중부 | `https://koreacentral.api.cognitive.microsoft.com` |
| | 미국 중북부 | `https://northcentralus.api.cognitive.microsoft.com` |
| | 북유럽 | `https://northeurope.api.cognitive.microsoft.com` |
| | 남아프리카 북부 | `https://southafricanorth.api.cognitive.microsoft.com` |
| | 미국 중남부 | `https://southcentralus.api.cognitive.microsoft.com` |
| | 동남아시아 | `https://southeastasia.api.cognitive.microsoft.com` |
| | 영국 남부 | `https://uksouth.api.cognitive.microsoft.com` |
| | 미국 중서부 | `https://westcentralus.api.cognitive.microsoft.com` |
| | 서유럽 | `https://westeurope.api.cognitive.microsoft.com` |
| | 미국 서부 | `https://westus.api.cognitive.microsoft.com` |
| | 미국 서부 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | US Gov 버지니아 | `https://virginia.api.cognitive.microsoft.us` |
| 중국 | 중국 동부 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | 중국 북부 | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>참조

* [코그너티브 서비스는 무엇입니까?](Welcome.md)
* [인증](authentication.md)
