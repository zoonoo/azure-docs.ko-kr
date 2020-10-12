---
title: Redis 용 Azure Cache의 새로운 기능
description: Redis 용 Azure 캐시에 대 한 최신 업데이트
author: yegu-ms
ms.service: cache
ms.topic: reference
ms.date: 09/28/2020
ms.author: yegu
ms.openlocfilehash: b30e83b89b25e6400b8c7e0419406631fa1edcd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91492537"
---
# <a name="whats-new-in-azure-cache-for-redis"></a>Redis 용 Azure Cache의 새로운 기능

## <a name="azure-tls-certificate-change"></a>Azure TLS 인증서 변경

Microsoft는 다른 Ca (인증 기관) 집합에서 TLS 서버 인증서를 사용 하도록 Azure 서비스를 업데이트 하 고 있습니다. 이 변경은 2020 년 8 월 13 일에서 2020 (예상)까지부터 출시 됩니다. [현재 ca 인증서가 ca/브라우저 포럼 기준 요구 사항 중 하나를 준수 하지](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)않기 때문에 Azure에서이 변경을 수행 합니다. 이 문제는 2020 년 7 월 1 일에 보고 되었으며 전 세계적으로 널리 사용 되는 여러 PKI (공개 키 인프라) 공급자에 적용 됩니다. 현재 Azure 서비스에서 사용 하는 대부분의 TLS 인증서는 *Baltimore CyberTrust Root* PKI에서 제공 됩니다. Azure Cache for Redis 서비스는 Baltimore CyberTrust Root에 계속 연결 됩니다. 그러나 해당 TLS 서버 인증서는 2020 년 10 월 12 일에 시작 되는 새로운 ICAs (중간 인증 기관)에서 발급 됩니다.

> [!NOTE]
> 이 변경은 공용 [Azure 지역의](https://azure.microsoft.com/global-infrastructure/geographies/)서비스로 제한 됩니다. 소 버린 (예: 중국) 또는 정부 클라우드가 제외 됩니다.
>
>

### <a name="does-this-change-affect-me"></a>이 변경이 내게 영향을 주나요?

Redis 고객에 대 한 대부분의 Azure 캐시가 변경의 영향을 받지 않습니다. 허용 되는 인증서 목록을 명시적으로 지정 하는 경우 응용 프로그램에 영향을 줄 수 있습니다. 예를 들어 "인증서 고정" 이라고 합니다. Baltimore CyberTrust Root 대신 중간 또는 리프 인증서에 고정 되어 있는 경우 **즉시 작업을 수행** 하 여 인증서 구성을 변경 해야 합니다.

다음 표에서는 롤업되는 인증서에 대 한 정보를 제공 합니다. 응용 프로그램에서 사용 하는 인증서에 따라 Redis 인스턴스에 대 한 Azure 캐시 연결의 손실을 방지 하기 위해 업데이트 해야 할 수 있습니다.

| CA 유형 | 현재 | 롤링 후 (2020 년 10 월 12 일) | 작업 |
| ----- | ----- | ----- | ----- |
| Root | 지문: d4de20d05e66fc53fe1a50882c78db2852cae474<br><br> 만료: 월요일, 5 월 12 2025 일, 4:59:00 PM<br><br> 주체 이름:<br> CN = Baltimore CyberTrust Root<br> OU = CyberTrust<br> O = Baltimore<br> C = IE | 변경 안 함 | 없음 |
| Intermediates | 지문<br> CN = Microsoft IT TLS CA 1<br> 지문: 417e225037fbfaa4f95761d5ae729e1aea7e3a42<br><br> CN = Microsoft IT TLS CA 2<br> 지문: 54d9d20239080c32316ed9ff980a48988f4adf2d<br><br> CN = Microsoft IT TLS CA 4<br> 지문: 8a38755d0996823fe8fa3116a277ce446eac4e99<br><br> CN = Microsoft IT TLS CA 5<br> 지문: Ad898ac73df333eb60ac1f5fc6c4b2219ddb79b7<br><br> 만료: 금요일, 5 월 20 일, 2024 5:52:38 AM<br><br> 주체 이름:<br> OU = Microsoft IT<br> O = Microsoft Corporation<br> L = Redmond<br> S = 워싱턴<br> C = US<br> | 지문<br> CN = Microsoft RSA TLS CA 01<br> 지문: 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a<br><br> CN = Microsoft RSA TLS CA 02<br> 지문: b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75<br><br> 만료: 오전 8 월 8 일 화요일, 오전 2024 시 12:00:00<br><br> 주체 이름:<br> O = Microsoft Corporation<br> C = US<br> | 필수 |

### <a name="what-actions-should-i-take"></a>어떤 작업을 수행 해야 하나요?

응용 프로그램에서 운영 체제 인증서 저장소를 사용 하거나 Baltimore 루트를 다른 곳으로 고정 하는 경우에는 아무 작업도 필요 하지 않습니다. 반면에 응용 프로그램이 중간 또는 리프 TLS 인증서를 고정 하는 경우 다음 루트를 고정 하는 것이 좋습니다.

| 인증서 | Thumbprint |
| ----- | ----- |
| [Baltimore 루트 CA](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 |
| [Digicert 전역 루트 G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |

> [!TIP]
> 중간 및 리프 인증서가 모두 자주 변경 될 것으로 예상 됩니다. 종속성을 사용 하지 않는 것이 좋습니다. 대신 응용 프로그램을 루트 인증서에 고정 하는 것은 덜 자주 롤오버 되기 때문입니다.
>
>

중간 인증서를 계속 고정 하려면 고정 된 중간 인증서 목록에 다음을 추가 합니다. 여기에는 추가 작업이 포함 되어 향후 변경 내용을 최소화 합니다.

| CA의 일반 이름 | Thumbprint |
| ----- | ----- |
| [Microsoft RSA TLS CA 01](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt) | 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a |
| [Microsoft RSA TLS CA 02](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt) | b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75 |
| [TLS 발급 CA 01 Microsoft Azure](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.cer) | 2f2877c5d778c31e0f29c7e371df5471bd673173 |
| [Microsoft Azure TLS 발급 CA 02](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.cer) | e7eea674ca718e3befd90858e09f8372ad0ae2aa |
| [Microsoft Azure TLS 발급 CA 05](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.cer) | 6c3af02e7f269aa73afd0eff2a88a4a1f04ed1e5 |
| [Microsoft Azure TLS 발급 CA 06](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer) | 30e01761ab97e59a06b41ef20af6f2de7ef4f7b0 |

응용 프로그램이 코드에서 인증서의 유효성을 검사 하는 경우 새로 고정 된 인증서의 속성 (예: 발급자, 지문)을 인식 하도록 수정 해야 합니다. 이 추가 확인은 모든 고정 된 인증서를 더 미래를 증명 하는 데 포함 해야 합니다.

## <a name="next-steps"></a>다음 단계

추가 질문이 있는 경우 [지원](https://azure.microsoft.com/support/options/)센터에 문의 하세요.  
