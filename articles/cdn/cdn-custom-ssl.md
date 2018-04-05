---
title: Azure Content Delivery Network 사용자 지정 도메인에서 HTTPS 구성 | Microsoft Docs
description: 사용자 지정 도메인의 Azure CDN 끝점에서 HTTPS를 활성화하거나 비활성화하도록 설정하는 방법을 알아봅니다.
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: casoper
ms.openlocfilehash: fea7121fc67944b20b8f39007edb0c0aad86aeaa
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="configure-https-on-an-azure-content-delivery-network-custom-domain"></a>Azure Content Delivery Network 사용자 지정 도메인에서 HTTPS 구성 

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Microsoft는 Azure CDN(Content Delivery Network)에서 사용자 지정 도메인에 대해 HTTPS 프로토콜을 지원합니다. HTTPS 사용자 지정 도메인을 지원하므로 자신의 고유한 도메인 이름을 사용하는 SSL로 안전한 콘텐츠를 제공하고 전송 중에 데이터 보안을 강화할 수 있습니다. 사용자 지정 도메인에 대해 HTTPS를 사용하는 워크플로는 클릭 한 번 사용, 완전한 인증서 관리를 통해 간소화되며 이 모든 것을 추가 비용 없이 수행할 수 있습니다.

전송 중에 웹 응용 프로그램의 중요한 데이터에 대해 프라이버시 및 무결성을 보장하는 것이 중요합니다. HTTPS 프로토콜을 사용하면 인터넷 간에 전송되는 중요한 데이터를 암호화할 수 있습니다. 신뢰할 수 있는 인증을 제공하며 공격으로부터 웹 응용 프로그램을 보호합니다. Azure CDN은 기본적으로 CDN 끝점에서 HTTPS를 지원합니다. 예를 들어 Azure CDN(예: https:\//contoso.azureedge.net)에서 CDN 끝점을 만드는 경우 HTTPS가 자동으로 사용하도록 설정됩니다. 또한 사용자 지정 도메인 HTTPS 지원을 통해 사용자 지정 도메인(예: https:\//www.contoso.com)에 대해서도 보안 전달을 사용할 수 있습니다. 

HTTPS 기능의 몇 가지 주요 특성은 다음과 같습니다.

- 추가 비용 없음: 인증서 얻기 또는 갱신에 대해 비용이 없으며 HTTPS 트래픽에 대한 추가 비용이 없습니다. CDN에서 송신되는 양(GB)에 대한 비용만 지불합니다.

- 간단한 사용: [Azure Portal](https://portal.azure.com)에서 한 번 클릭으로 프로비전을 사용할 수 있습니다. REST API 또는 기타 개발자 도구를 사용하여 기능을 활성화할 수도 있습니다.

- 완전한 인증서 관리: 사용자를 위해 모든 인증서 조달 및 관리가 처리됩니다. 만료되기 전에 인증서가 자동으로 프로비전되고 갱신되므로 인증서 만료로 인해 서비스가 중단될 위험이 없습니다.

>[!NOTE] 
>HTTPS 지원을 사용하도록 설정하기 전에 [Azure CDN 사용자 지정 도메인](./cdn-map-content-to-custom-domain.md)을 미리 설정해야 합니다.

## <a name="enabling-https"></a>HTTPS 활성화

사용자 지정 도메인에서 HTTPS를 활성화하려면 다음 단계를 따르세요.

### <a name="step-1-enable-the-feature"></a>1단계: 기능 활성화 

1. [Azure Portal](https://portal.azure.com)에서 Verizon 표준 또는 프리미엄 CDN 프로필로 이동합니다.

2. 끝점 목록에서 사용자 지정 도메인을 포함하는 끝점을 클릭합니다.

3. HTTPS를 사용하도록 설정할 사용자 지정 도메인을 클릭합니다.

    ![사용자 지정 도메인 목록](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. **켜기**를 클릭하여 HTTPS를 활성화한 다음 **적용**을 클릭합니다.

    ![사용자 지정 도메인 HTTPS 상태](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


### <a name="step-2-validate-domain"></a>2단계: 도메인 유효성 검사

>[!NOTE]
>DNS 공급자가 포함된 CAA(Certificate Authority Authorization) 레코드가 있으면 DigiCert가 유효한 CA로 포함되어야 합니다. CAA 레코드를 사용하면 도메인 소유자가 자신의 도메인에 대한 인증서를 발급할 권한이 있는 CA를 DNS 공급자로 지정할 수 있습니다. CA에서 CAA 레코드가 있는 도메인에 대한 인증서 주문을 받고 해당 CA가 인증된 발급자로 나열되지 않은 경우 해당 도메인 또는 하위 도메인에 인증서를 발급할 수 없습니다. CAA 레코드 관리에 대한 내용은 [CAA 레코드 관리](https://support.dnsimple.com/articles/manage-caa-record/)를 참조하세요. CAA 레코드 도구는 [CAA 레코드 도우미](https://sslmate.com/caa/)를 참조하세요.

#### <a name="custom-domain-is-mapped-to-cdn-endpoint"></a>사용자 지정 도메인이 CDN 끝점에 매핑됨

끝점에 사용자 지정 도메인을 추가한 경우, 도메인 등록 기관의 DNS 테이블에 CDN 끝점 호스트 이름에 매핑할 CNAME 레코드를 만들었습니다. 해당 CNAME 레코드가 여전히 있고 cdnverify 하위 도메인을 포함하지 않는 경우 DigiCert CA(인증 기관)는 이 레코드를 사용하여 사용자 지정 도메인의 소유권을 확인합니다. 

CNAME 레코드는 다음 형식이어야 합니다. 여기서 *Name*은 사용자 지정 도메인 이름이고 *Value*는 CDN 끝점 호스트 이름입니다.

| Name            | 형식  | 값                 |
|-----------------|-------|-----------------------|
| www.contoso.com | CNAME | contoso.azureedge.net |

CNAME 레코드에 대한 자세한 내용은 [CNAME DNS 레코드 만들기](https://docs.microsoft.com/en-us/azure/cdn/cdn-map-content-to-custom-domain#step-2-create-the-cname-dns-records)를 참조하세요.

CNAME 레코드가 올바른 형식이면 DigiCert는 사용자 지정 도메인 이름을 자동으로 확인하고 SAN(주체 대체 이름) 인증서에 추가합니다. DigitCert는 확인 메일을 보내지 않으며 요청을 승인할 필요가 없습니다. 인증서는 1년 동안 유효하며 만료되기 전에 자동으로 갱신됩니다. [3단계: 전파 대기](#step-3-wait-for-propagation)에서 계속합니다. 

#### <a name="cname-record-is-not-mapped-to-cdn-endpoint"></a>CNAME 레코드가 CDN 끝점에 매핑되지 않음

끝점에 대한 CNAME 레코드 항목이 더 이상 없거나 cdnverify 하위 도메인을 포함하는 경우 이 단계의 나머지 지침을 따르세요.

사용자 지정 도메인에서 HTTPS를 활성화한 후에 DigiCert CA(인증 기관)에서 도메인의 [WHOIS](http://whois.domaintools.com/) 등록자 정보에 따라 등록자에게 연락하여 도메인 소유권에 대한 유효성을 검사합니다. 기본적으로 WHOIS에 등록된 전자 메일 주소 또는 전화 번호를 통해 연락합니다. 사용자 지정 도메인에서 HTTPS를 활성화하기 전에 도메인 유효성 검사를 완료해야 합니다. 업무일 기준 6일 이내 도메인이 승인되어야 합니다. 6 영업일 이내에 승인되지 않은 요청은 자동으로 취소됩니다. 

![WHOIS 레코드](./media/cdn-custom-ssl/whois-record.png)

또한 DigiCert는 추가 이메일 주소로 확인 전자 메일을 보냅니다. WHOIS 등록자 정보가 비공개인 경우 다음 주소 중 하나에서 직접 승인할 수 있는지 확인합니다.

admin@&lt;your-domain-name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
webmaster@&lt;your-domain-name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
postmaster@&lt;your-domain-name.com&gt;  

잠시 후 다음 예제와 비슷한 승인 요청 전자 메일을 받게 됩니다. 스팸 필터를 사용하는 경우 admin@digicert.com을 허용 목록에 추가입니다. 24시간 이내에 전자 메일을 받지 못하면 Microsoft 지원에 문의하세요.
    
![도메인 유효성 검사 전자 메일](./media/cdn-custom-ssl/domain-validation-email.png)

승인 링크를 클릭하는 경우 다음과 같은 온라인 승인 양식으로 이동됩니다. 
    
![도메인 유효성 검사 양식](./media/cdn-custom-ssl/domain-validation-form.png)

양식의 지침을 따르세요. 다음과 같이 두 가지 확인 옵션이 있습니다.

- 동일한 루트 도메인의 동일한 계정을 통해 이루어진 모든 향후 주문을 승인할 수 있습니다(예: consoto.com). 이 방법은 동일한 루트 도메인에 대한 추가 사용자 지정 도메인을 추가하려는 경우에 권장됩니다.

- 이 요청에 사용된 특정 호스트 이름을 승인할 수 있습니다. 후속 요청에는 추가 승인이 필요합니다.

승인 후에는 DigiCert가 사용자 지정 도메인 이름을 SAN 인증서에 추가합니다. 인증서는 1년 동안 유효하며 만료되기 전 자동으로 갱신됩니다.

### <a name="step-3-wait-for-propagation"></a>3단계: 전파 대기

도메인 이름이 확인된 후 사용자 지정 도메인 HTTPS 기능이 활성 상태가 될 때까지는 최대 6-8시간 소요됩니다. 프로세스가 완료되면 Azure Portal에서 사용자 지정 HTTPS 상태가 **사용**으로 설정되고 사용자 지정 도메인 대화 상자의 네 가지 작업 단계가 '완료'로 표시됩니다. 사용자 지정 도메인은 이제 HTTPS를 활성화할 준비가 되었습니다.

![HTTPS 대화 상자 활성화](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>작업 진행 상태

다음 표에는 HTTPS를 활성화하도록 설정했을 때 나타나는 작업 진행률을 표시합니다. HTTPS를 사용하도록 설정하면 네 가지 작업 단계가 사용자 지정 도메인 대화 상자에 나타납니다. 각 단계가 활성화되면 진행되는 동안 단계 아래에 추가 하위 단계 정보가 표시됩니다. 이러한 하위 단계가 모두 발생하지는 않습니다. 단계가 성공적으로 완료되면 옆에 녹색 확인 표시가 나타납니다. 

| 작업 단계 | 작업 하위 단계 정보 | 
| --- | --- |
| 1 요청 제출 | 요청 제출 |
| | HTTPS 요청을 제출하는 중입니다. |
| | HTTPS 요청이 제출되었습니다. |
| 2 도메인 유효성 검사 | 도메인 소유권 확인을 요구하는 전자 메일을 보냈습니다. 귀하의 확인을 기다리는 중입니다. ** |
| | 도메인 소유권을 확인했습니다. |
| | 도메인 소유권 유효성 검사 요청이 만료되었습니다(고객이 6일 이내의 응답하지 않았음). 도메인에서 HTTPS를 활성화할 수 없습니다. * |
| | 고객에 의해 도메인 소유권 유효성 검사 요청이 거부되었습니다. 도메인에서 HTTPS를 활성화할 수 없습니다. * |
| 3 인증서 프로비전 | 인증 기관이 현재 도메인에서 HTTPS를 활성화하는 데 필요한 인증서를 발급 중입니다. |
| | 인증서가 발급되었고 현재 CDN 네트워크로 배포되고 있습니다. 최대 6시간이 걸릴 수 있습니다. |
| | 인증서가 CDN 네트워크에 배포되었습니다. |
| 4 완료 | 도메인에서 HTTPS를 활성화하도록 설정했습니다. |

\* 오류가 발생하지 않으면 이 메시지가 표시되지 않습니다. 

\** CDN 끝점 호스트 이름을 직접 가리키는 사용자 지정 도메인에 대한 CNAME 항목이 있는 경우에는 이 메시지가 나타나지 않습니다.

요청을 제출하기 전에 오류가 발생하는 경우 다음과 같은 오류 메시지가 표시됩니다.

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="disabling-https"></a>HTTPS 비활성화

사용자 지정 도메인에서 HTTPS를 활성화한 후 나중에 비활성화할 수 있습니다. HTTPS를 비활성화하려면 다음 단계를 따르세요.

### <a name="step-1-disable-the-feature"></a>1단계: 기능 비활성화 

1. [Azure Portal](https://portal.azure.com)에서 Verizon 표준 또는 프리미엄 CDN 프로필로 이동합니다.

2. 끝점 목록에서 사용자 지정 도메인을 포함하는 끝점을 클릭합니다.

3. HTTPS를 비활성화하도록 설정할 사용자 지정 도메인을 클릭합니다.

    ![사용자 지정 도메인 목록](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. **끄기**를 클릭하여 HTTPS를 비활성화한 다음 **적용**을 클릭합니다.

    ![사용자 지정 HTTPS 대화 상자](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="step-2-wait-for-propagation"></a>2단계: 전파 대기

사용자 지정 도메인 HTTPS 기능을 비활성화한 후 최대 6-8시간까지 걸릴 수 있습니다. 프로세스가 완료되면 Azure Portal에서 사용자 지정 HTTPS 상태가 **사용 안 함**으로 설정되고 사용자 지정 도메인 대화 상자의 세 가지 작업 단계가 '완료'로 표시됩니다. 사용자 지정 도메인은 더 이상 HTTPS를 사용할 수 없습니다.

![HTTPS 대화 상자 비활성화](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

### <a name="operation-progress"></a>작업 진행 상태

다음 표에는 HTTPS를 비활성화하도록 설정했을 때 나타나는 작업 진행률을 표시합니다. HTTPS를 사용하지 않도록 설정하면 세 가지 작업 단계가 사용자 지정 도메인 대화 상자에 나타납니다. 각 단계가 활성화되면 단계 아래에 추가 세부 정보가 표시됩니다. 단계가 성공적으로 완료되면 옆에 녹색 확인 표시가 나타납니다. 

| 작업 진행 상태 | 작업 세부 정보 | 
| --- | --- |
| 1 요청 제출 | 요청 제출 |
| 2 인증서 프로비전 해제 | 인증서 삭제 |
| 3 완료 | 인증서 삭제됨 |

## <a name="frequently-asked-questions"></a>질문과 대답

1. *인증서 공급자는 누구이며 어떤 유형의 인증서가 사용되나요?*

    Microsoft는 DigiCert에서 제공하는 SAN(주체 대체 이름) 인증서를 사용합니다. SAN 인증서는 하나의 인증서로 정규화된 여러 도메인 이름을 보호할 수 있습니다.

2. *전용 인증서를 사용할 수 있나요?*
    
    현재는 불가능하지만 준비 중입니다.

3. *DigiCert로부터 도메인 확인 메일을 받지 못한 경우 어떻게 하나요?*

    24시간 이내에 전자 메일을 받지 못하면 Microsoft에 문의하세요. 끝점 호스트 이름을 직접 가리키는 사용자 지정 도메인에 대한 CNAME 항목이 있고 cdnverify 하위 도메인 이름을 사용하지 않는 경우에는 도메인 확인 메일이 수신되지 않습니다. 유효성 검사가 자동으로 수행됩니다.

4. *SAN 인증서를 사용하는 것보다 전용 인증서를 사용하는 것이 더 안전한가요?*
    
    SAN 인증서는 전용 인증서와 동일한 암호화 및 보안 표준을 따릅니다. 발급된 모든 SSL 인증서는 향상된 서버 보안을 위해 SHA-256을 사용합니다.

5. *Akamai의 Azure CDN에서 사용자 지정 도메인 HTTPS를 사용할 수 있나요?*

    현재 이 기능은 Verizon의 Azure CDN에만 사용할 수 있습니다. Microsoft는 앞으로 몇 달 안에 Akamai의 Azure CDN을 통해 이 기능을 지원하기 위해 노력하고 있습니다.

6. *내 DNS 공급자에게 CAA(Certificate Authority Authorization) 레코드가 필요합니까?*

    아니요, CAA 레코드는 현재 필요하지 않습니다. 그러나 이 레코드가 있으면 DigiCert가 유효한 CA로 포함되어야 합니다.


## <a name="next-steps"></a>다음 단계

- [Azure CDN 끝점에서 사용자 지정 도메인을 사용하도록 설정](./cdn-map-content-to-custom-domain.md)하는 방법을 알아봅니다.


