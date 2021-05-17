---
title: Internet Analyzer FAQ | Microsoft Docs
description: Azure Internet Analyzer에 대한 FAQ입니다.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "74184265"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Azure Internet Analyzer FAQ(미리 보기)

Azure Internet Analyzer에 대한 FAQ입니다. 추가 질문이 있는 경우 [피드백 포럼](https://aka.ms/internetAnalyzerFeedbackForum)으로 이동하여 질문을 게시하세요. 자주 묻는 질문은 빠르고 쉽게 찾을 수 있도록 이 문서에 추가됩니다.

## <a name="how-do-i-participate-in-the-preview"></a>미리 보기에는 어떻게 참여할 수 있나요?

미리 보기는 일부 고객에게 제공됩니다. 미리 보기 가입에 관심이 있다면 다음을 수행하세요.

1. [Azure Portal](https://ms.portal.azure.com)에 로그인합니다.
2. **구독** 페이지로 이동합니다.
3. Internet Analyzer를 사용하려는 Azure 구독을 클릭합니다.
4. 구독의 **리소스 공급자** 설정으로 이동합니다.
5. **Microsoft.Network** 를 검색하고 **등록**(또는 **다시 등록**) 단추를 클릭합니다.
![액세스 요청](./media/ia-faq/request-preview-access.png)

6. 이메일 주소와 액세스 요청에 사용한 Azure 구독 ID를 제공하면 [요청 승인](https://aka.ms/internetAnalyzerContact)이 이루어집니다.
7. 요청이 승인되면 확인 이메일을 받게 되며, 새로 허용된 Azure 구독에서 Internet Analyzer 리소스를 생성/업데이트/수정할 수 있습니다.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>테스트를 실행하기 위해 클라이언트를 포함해야 하나요?

예, 사용자와 관련된 메트릭을 수집하려면 Internet Analyzer 클라이언트를 애플리케이션에 설치해야 합니다. [클라이언트를 설치하는 방법을 알아보세요.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>미리 보기 참여에 요금이 청구되나요?
아니요, Azure Internet Analyzer는 미리 보기 상태에서 무료로 사용할 수 있습니다. 미리 보기 중에는 서비스 수준 계약이 없습니다.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Internet Analyzer는 어떤 시나리오를 해결하도록 설계되었나요?

Internet Analyzer는 사용자 인구를 기준으로 네트워킹 성능 정보를 제공하도록 설계되었습니다. 사용자에게 가장 적합한 성능 결정을 내리는 데 도움이 되도록 Internet Analyzer는 고유한 사용자 인구를 사용하여 두 인터넷 엔드포인트의 성능을 비교합니다. 인터넷 분석기는 여러 가지 질문에 대답할 수 있지만 가장 일반적인 질문은 다음과 같습니다.

* 클라우드로 마이그레이션할 때 성능에 어떤 영향을 미치나요? 
    * *제안된 테스트: 사용자 지정(현재 온-프레미스 인프라) 및 Azure(미리 구성된 아무 엔드포인트)*
* 데이터를 에지에 배치하는 것과 데이터 센터에 배치하는 것은 어떤 가치가 있나요? 
    *  *제안된 테스트: Azure 및 Azure Front Door, Azure 및 Microsoft의 Azure CDN*
* Azure Front Door의 성능 혜택은 무엇인가요?
    *  *제안된 테스트: 사용자 지정/ Azure/ CDN 및 Azure Front Door*
* Microsoft의 Azure CDN이 주는 성능 혜택은 무엇인가요? 
    *  *제안된 테스트: 사용자 지정/ Azure/ AFD 및 Microsoft의 Azure CDN*
* Microsoft의 Azure CDN은 어떤 측면에서 유사한 성능 혜택을 제공하나요? 
    *  *제안된 테스트: 사용자 지정(기타 CDN 엔드포인트) 및 Microsoft의 Azure CDN*
* 각 지역의 최종 사용자 모집단에 가장 적합한 클라우드는 무엇인가요? 
    *  *제안된 테스트: 사용자 지정(기타 클라우드 서비스) 및 Azure(미리 구성된 아무 엔드포인트)*

## <a name="which-tests-can-i-run-in-preview"></a>미리 보기 상태에서 실행할 수 있는 테스트는 무엇인가요?

Internet Analyzer에서 만드는 각 테스트는 엔드포인트 A와 엔드포인트 B라는 두 엔드포인트로 구성됩니다. 다음 조합을 테스트로 실행할 수 있습니다.  
* 미리 구성된 두 엔드포인트
* 사용자 지정 엔드포인트 하나와 미리 구성된 엔드포인트 하나 또는
* 두 개의 [사용자 지정 엔드포인트](internet-analyzer-custom-endpoint.md)(사용자 지정 엔드포인트 하나는 Azure에 있어야 함)

미리 보기 중에는 다음과 같은 미리 구성된 엔드포인트를 사용할 수 있습니다.
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

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Internet Analyzer는 Azure에서 제공하는 다른 모니터링 서비스와 어떻게 다른가요?

Internet Analyzer는 최종 사용자의 성능을 이해하는 데 도움이 되며, 최종 사용자의 성능을 향상시키기 위한 결정을 내리도록 지원합니다. 다른 Azure 모니터링 도구는 Azure 서비스에 대한 인사이트를 제공하지만 Internet Analyzer는 사용자의 엔드투엔드 인터넷 성능을 측정하는 데 집중합니다.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Internet Analzyer에서 측정 데이터는 어떻게 처리되나요?

Azure는 [강력한 보안 프로세스를 가지고 광범위한 규정 준수 표준을 충족](https://azure.microsoft.com/support/trust-center/)합니다. 사용자와 지정된 팀만 데이터에 액세스할 수 있습니다. Microsoft 직원은 지식으로 제한된 특정 상황에서만 해당 데이터에 제한된 액세스 권한을 갖을 수 있습니다. 전송 중 및 미사용 데이터는 암호화됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Internet Analyzer 개요](internet-analyzer-overview.md)를 참조하세요.
