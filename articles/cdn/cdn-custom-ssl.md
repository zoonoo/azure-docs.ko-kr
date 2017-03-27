---
title: "Azure CDN 사용자 지정 도메인에서 HTTPS를 사용하도록 설정 | Microsoft Docs"
description: "사용자 지정 도메인의 Azure CDN 끝점에서 HTTPS를 사용하도록 설정하는 방법을 알아봅니다."
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: b334ba6bbec1d0a7e23a514174bffae01c7fff05
ms.lasthandoff: 03/04/2017


---
# <a name="enable-https-on-an-azure-cdn-custom-domain"></a>Azure CDN 사용자 지정 도메인에서 HTTPS를 사용하도록 설정

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Azure CDN 사용자 지정 도메인에 대한 HTTPS 지원을 통해 자신의 고유한 도메인 이름을 사용하는 SSL로 안전한 콘텐츠를 제공하고 전송 중에 데이터 보안을 강화할 수 있습니다. 사용자 지정 도메인에 대해 HTTPS를 사용하는 종단 간 워크플로는 클릭 한 번 사용, 완전한 인증서 관리를 통해 간소화되며 이 모든 것을 추가 비용 없이 수행할 수 있습니다.

전송 중에 모든 웹 응용 프로그램의 중요한 데이터에 대해 프라이버시 및 무결성을 보장하는 것이 중요합니다. HTTPS 프로토콜을 사용하면 인터넷 간에 전송되는 중요한 데이터를 암호화할 수 있습니다. 신뢰할 수 있는 인증을 제공하며 공격으로부터 웹 응용 프로그램을 보호합니다. 현재 Azure CDN은 CDN 끝점에서 HTTPS를 지원합니다. 예를 들어 Azure CDN(예: https://contoso.azureedge.net )에서 CDN 끝점을 만드는 경우 기본적으로 HTTPS가 사용하도록 설정됩니다. 이제 사용자 지정 도메인 HTTPS를 사용하여 사용자 지정 도메인(예: https://www.contoso.com )에 대해서도 안전한 전달을 사용할 수 있습니다. 

HTTPS 기능의 주요 특성 몇 가지는 다음과 같습니다.

- 추가 비용 없음: 인증서 얻기 또는 갱신에 대해 비용이 없으며 HTTPS 트래픽에 대한 추가 비용이 없습니다. CDN에서 송신되는 양(GB)에 대한 비용만 지불합니다.

- 간단한 사용: [Azure Portal](https://portal.azure.com)에서 클릭 한 번으로 프로비전이 가능합니다. REST API 또는 기타 개발자 도구를 사용하여 기능을 활성화할 수도 있습니다.

- 완전한 인증서 관리: 사용자를 위해 모든 인증서 조달 및 관리가 처리됩니다. 인증서가 만료되기 전에 자동으로 프로비전 및 갱신됩니다. 따라서 인증서 만료로 인한 서비스 중단 위험이 완전히 제거됩니다.

>[!NOTE] 
>HTTPS 지원을 사용하도록 설정하기 전에 [Azure CDN 사용자 지정 도메인](./cdn-map-content-to-custom-domain.md)을 미리 설정해야 합니다.

## <a name="step-1-enabling-the-feature"></a>1단계: 기능 활성화 

1. [Azure Portal](https://portal.azure.com)에서 Verizon 표준 또는 프리미엄 CDN 프로필로 이동합니다.

2. 끝점 목록에서 사용자 지정 도메인을 포함하는 끝점을 클릭합니다.

3. HTTPS를 사용하도록 설정할 사용자 지정 도메인을 클릭합니다.

    ![끝점 블레이드](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. **설정**을 클릭하여 HTTPS를 사용하도록 설정하고 변경 내용을 저장합니다.

    ![사용자 지정 HTTPS 대화 상자](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


## <a name="step-2-domain-validation"></a>2단계: 도메인 유효성 검사

>[!IMPORTANT] 
>사용자 지정 도메인에서 HTTPS를 활성화하기 전에 도메인 유효성 검사를 완료해야 합니다. 업무일 기준 6일 이내 도메인이 승인되어야 합니다. 업무일 기준 6일 이내 도메인이 승인되지 않으면 요청이 취소됩니다.  

사용자 지정 도메인에서 HTTPS를 사용하도록 설정한 후에는, HTTPS 인증서 공급자 DigiCert가 도메인 등록자에게 연락하여 전자 메일(기본값) 또는 전화로 WHOIS 등록자 정보를 토대로 도메인 소유권의 유효성을 확인합니다. 또한 DigiCert는 아래 주소로 확인 전자 메일을 보냅니다. WHOIS 등록자 정보가 개인인 경우 이러한 주소 중 하나에서 직접 승인할 수 있는지 확인합니다.

>admin@<your-domain-name.com> administrator@<your-domain-name.com>  
>webmaster@<your-domain-name.com>  
>hostmaster@<your-domain-name.com>  
>postmaster@<your-domain-name.com>


전자 메일을 받으면 두 가지 확인 옵션이 제공됩니다.

1. 동일한 루트 도메인의 동일한 계정을 통해 이루어진 모든 향후 주문을 승인할 수 있습니다(예: consoto.com). 향후 동일한 루트 도메인에 대해 사용자 지정 도메인을 추가할 예정인 경우 권장되는 방법입니다.
 
2. 이 요청에 사용된 특정 호스트 이름을 승인할 수 있습니다. 이후 요청에 대해 추가 승인이 필요합니다.

    전자 메일 예:
    
    ![사용자 지정 HTTPS 대화 상자](./media/cdn-custom-ssl/domain-validation-email-example.png)

승인 후에는 DigiCert가 사용자 지정 도메인 이름을 SAN 인증서에 추가합니다. 인증서는&1;년 동안 유효하며 만료되기 전 자동으로 갱신됩니다.

## <a name="step-3-wait-for-the-propagation-then-start-using-your-feature"></a>3단계: 적용될 때까지 기다린 후 기능 사용 시작

도메인 이름이 확인된 후 사용자 지정 도메인 HTTPS 기능이 활성 상태가 될 때까지는 최대 6-8시간 소요됩니다. 프로세스가 완료되면 Azure Portal에서 "사용자 지정 HTTPS" 상태가 "사용"으로 설정됩니다. 이제 사용자 지정 도메인이 있는 HTTPS를 사용할 준비가 됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

1. *인증서 공급자는 누구이며 어떤 유형의 인증서가 사용되나요?*

    DigiCert가 제공한 주체 대체 이름(SAN) 인증서를 사용합니다. SAN 인증서는 한 개의 인증서로 여러 정규화된 도메인 이름을 보안 지정할 수 있습니다.

2. *전용 인증서를 사용할 수 있나요?*
    
    현재는 불가능하지만 준비 중입니다.

3. *DigiCert로부터 도메인 확인 메일을 받지 못한 경우 어떻게 하나요?*

    24시간 이네 전자 메일을 받지 못하면 Microsoft에 문의하세요.

4. *SAN 인증서를 사용하는 것보다 전용 인증서를 사용하는 것이 더 안전한가요?*
    
    SAN 인증서는 전용 인증서와 동일한 암호화 및 보안 표준을 따릅니다. 발급된 모든 SSL 인증서는 서버 보안 강화를 위해 SHA-256을 사용 중입니다.

5. *Akamai의 Azure CDN에서 사용자 지정 도메인 HTTPS를 사용할 수 있나요?*

    현재 이 기능은 Verizon의 Azure CDN에만 사용할 수 있습니다. Akamai의 Azure CDN에서 이 기능을 몇 달 이내 지원하기 위한 작업 중입니다.


## <a name="next-steps"></a>다음 단계

- [Azure CDN 끝점에서 사용자 지정 도메인을 사용하도록 설정](./cdn-map-content-to-custom-domain.md)하는 방법을 알아봅니다.



