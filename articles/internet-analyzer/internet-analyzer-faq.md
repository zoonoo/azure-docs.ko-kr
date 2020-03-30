---
title: 인터넷 분석기 자주 묻는 질문 | 마이크로 소프트 문서
description: Azure 인터넷 분석기의 FAQ입니다.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184265"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Azure 인터넷 분석기 자주 묻는 질문(미리 보기)

Azure 인터넷 분석기용 FAQ입니다. [feedback forum](https://aka.ms/internetAnalyzerFeedbackForum) 질문을 자주 하는 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가합니다.

## <a name="how-do-i-participate-in-the-preview"></a>미리 보기에 참여하려면 어떻게 해야 합니까?

미리 보기는 일부 고객을 위해 사용할 수 있습니다. 미리 보기에 참여하고 싶다면 다음을 수행하십시오.

1. [Azure 포털에](https://ms.portal.azure.com)로그인합니다.
2. 구독 페이지로 **이동합니다.**
3. 인터넷 분석기를 사용하려는 Azure 구독을 클릭합니다.
4. 구독의 **리소스 공급자** 설정으로 이동합니다.
5. **Microsoft.Network를** 검색하고 **레지스터(또는** **다시 등록)** 버튼을 클릭합니다.
![액세스 요청](./media/ia-faq/request-preview-access.png)

6. 액세스 요청을 하는 데 사용된 전자 메일 주소와 Azure 구독 ID를 제공하여 [승인을](https://aka.ms/internetAnalyzerContact) 요청합니다.
7. 요청이 승인되면 전자 메일 확인을 받게 되며 새로 허용된 Azure 구독에서 인터넷 분석기 리소스를 생성/업데이트/수정할 수 있습니다.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>테스트를 실행하기 위해 클라이언트를 포함해야 합니까?

예. 사용자별 메트릭을 수집하려면 응용 프로그램에 인터넷 분석기 클라이언트를 설치해야 합니다. [클라이언트를 설치하는 방법을 알아봅니다.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>미리 보기 참여에 대한 요금이 청구되나요?
아니요, Azure 인터넷 분석기는 미리 보기에서 무료로 사용할 수 있습니다. 미리 보기 중에 서비스 수준 계약이 없습니다.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>인터넷 분석기는 어떤 시나리오를 다루도록 설계됩니까?

인터넷 분석기는 사용자 집단을 기반으로 네트워킹 성능 통찰력을 제공하도록 설계되었습니다. 사용자에게 최상의 성능을 결정하는 데 도움이 되도록 인터넷 분석기는 고유한 사용자 집단을 사용하여 두 인터넷 끝점의 성능을 비교합니다. 인터넷 분석기는 여러 가지 질문에 대답할 수 있지만 가장 일반적인 질문은 다음과 같습니다.

* 클라우드로 마이그레이션할 때 성능에 어떤 영향을 미치나요? 
    * *권장 테스트: 사용자 지정(현재 온-프레미스 인프라) vs. Azure(미리 구성된 모든 끝점)*
* 데이터를 에지에 배치하는 것과 데이터 센터에 배치하는 것은 어떤 가치가 있나요? 
    *  *권장 테스트: Azure 대 Azure 정문, Azure 대 Azure cdN*
* Azure Front Door의 성능 혜택은 무엇인가요?
    *  *권장 테스트: 사용자 지정/ Azure/ CDN 대 Azure 정문*
* Microsoft의 Azure CDN이 주는 성능 혜택은 무엇인가요? 
    *  *권장 테스트: 사용자 지정/ Azure/ AFD 대 마이크로소프트에서 Azure CDN*
* Microsoft의 Azure CDN은 어떤 측면에서 유사한 성능 혜택을 제공하나요? 
    *  *권장 테스트: 사용자 지정(다른 CDN 끝점) 대 Microsoft의 Azure CDN*
* 각 지역의 최종 사용자 모집단에 가장 적합한 클라우드는 무엇인가요? 
    *  *권장 테스트: 사용자 지정(기타 클라우드 서비스) vs. Azure(미리 구성된 모든 끝점)*

## <a name="which-tests-can-i-run-in-preview"></a>미리 보기에서 실행할 수 있는 테스트는 무엇입니까?

인터넷 분석기에서 만드는 각 테스트는 끝점 A와 끝점 B의 두 끝점으로 구성됩니다. 다음 조합 중 어느 한 가지를 테스트로 실행할 수 있습니다.  
* 미리 구성된 두 개의 끝점,
* 하나의 사용자 지정 및 미리 구성된 끝점 하나 또는
* 두 개의 [사용자 지정 끝점(하나의](internet-analyzer-custom-endpoint.md) 사용자 지정 끝점이 Azure에 있어야 합니다).

미리 구성된 다음 끝점은 미리 미리 보기 중에 사용할 수 있습니다.
* **Azure 지역**
    * 브라질 남부
    * 인도 중부
    * 미국 중부
    * 동아시아
    * 미국 동부
    * 일본 서부
    * 북유럽
    * 남아프리카 북부
    * 동남아시아
    * 아랍에미리트 북부
    * 영국 서부  
    * 서유럽
    * 미국 서부
    * 미국 서부 2
* **여러 Azure 지역 조합**
    * 미국 동부, 브라질 남부
    * 미국 동부, 동아시아
    * 서유럽, 브라질 남부
    * 서유럽, 동남 아시아
    * 서유럽, 아랍에미리트 북부
    * 미국 서부, 미국 동부
    * 미국 서부, 서유럽
    * 미국 서부, 아랍에미리트 북부
    * 서유럽, 아랍에미리트 북부, 동남 아시아
    * 미국 서부, 서유럽, 동아시아
    * 미국 서부, 북유럽, 동남 아시아, 아랍에미리트 북부, 남아프리카 북부 
* **Azure + Azure Front Door** - 위에 나열된 단일 또는 여러 Azure 지역 조합에 배포됩니다.
* **Azure + Microsoft의 Azure CDN** - 위에 나열된 단일 Azure 지역 조합에 배포됩니다.
* **Azure + Azure Traffic Manager** - 위에 나열된 여러 Azure 지역 조합에 배포됩니다.

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>인터넷 분석기는 Azure에서 제공하는 다른 모니터링 서비스와 어떻게 다른가요?

인터넷 분석기는 최종 사용자의 성능을 이해하고 성능을 개선하기 위한 의사 결정을 내리는 데 도움이 됩니다. 다른 Azure 모니터링 도구는 Azure 서비스에 대한 통찰력을 제공하지만 인터넷 분석기는 사용자를 위한 종단 간 인터넷 성능을 측정하는 데 중점을 둡니다.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>인터넷 분석기에서 측정 데이터를 어떻게 처리합니까?

Azure는 [강력한 보안 프로세스를 가지고 광범위한 규정 준수 표준을 충족](https://azure.microsoft.com/support/trust-center/)합니다. 사용자와 지정된 팀만 데이터에 액세스할 수 있습니다. Microsoft 직원은 지식으로 제한된 특정 상황에서만 해당 데이터에 제한된 액세스 권한을 갖을 수 있습니다. 그것은 전송 및 나머지에서 암호화됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 인터넷 [분석기 개요를](internet-analyzer-overview.md)참조하십시오.
