---
title: Azure Active Directory 인증 기관
description: Azure에서 사용되는 신뢰할 수 있는 인증서 목록
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bea6f4161bcca063cd2c58d4c463473426f159ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96859134"
---
# <a name="certificate-authorities-used-by-azure-active-directory"></a>Azure Active Directory에서 사용하는 인증 기관

> [!IMPORTANT]
> 이 페이지의 정보는 허용되는 CA(인증 기관) 목록을 명시적으로 지정하는 엔터티와 관련이 있습니다. 인증서 고정이라고 하는 이 방법은 다른 옵션이 없는 경우가 아니라면 피해야 합니다.

TLS/SSL 프로토콜을 통해 Azure AD(Azure Active Directory) ID 서비스에 액세스하려는 모든 엔터티는 아래 나열된 CA의 인증서를 제공합니다. 엔터티가 해당 CA를 신뢰하는 경우 인증서를 사용하여 ID 서비스의 ID와 적법성을 확인하고 보안 연결을 설정할 수 있습니다.

인증 기관은 루트 CA 및 중간 CA로 분류할 수 있습니다. 일반적으로 루트 CA에는 하나 이상의 중간 CA가 연결되어 있습니다. 이 문서에는 Azure AD ID 서비스에서 사용하는 루트 CA와 각 루트와 연결된 중간 CA가 나열됩니다. 각 CA에 URI(Uniform Resource Identifier)를 포함하여 연결된 AIA(권한 정보 액세스) 파일과 CDP(인증서 해지 목록 배포 지점) 파일을 다운로드합니다. 해당하는 경우에는 OCSP(온라인 인증서 상태 프로토콜) 엔드포인트에 대한 URI도 제공합니다.

## <a name="cas-used-in-azure-public-and-azure-us-government-clouds"></a>Azure 퍼블릭 클라우드 및 Azure 미국 정부 클라우드에서 사용되는 CA

서비스마다 다른 루트 또는 중간 CA를 사용할 수 있습니다. 따라서 아래 나열된 모든 항목이 필요할 수 있습니다.

### <a name="digicert-global-root-g2"></a>DigiCert Global Root G2


| 루트 CA| 일련 번호| 발급 날짜 만료 날짜| SHA1 지문| URI |
| - |- |-|-|-|-|
| DigiCert Global Root G2| 033af1e6a711a 9a0bb2864b11d09fae5| 2013년 8월 1일 <br>2038년 1월 15일| df3c24f9bfd666761b268 073fe06d1cc8d4f82a4| [AIA](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertGlobalRootG2.crl) |


#### <a name="associated-intermediate-cas"></a>연결된 중간 CA

| 발급 및 중간 CA| 일련 번호| 발급 날짜 만료 날짜| SHA1 지문| URI |
| - | - | - | - | - | 
| Microsoft Azure TLS Issuing CA 01| 0aafa6c5ca63c45141 ea3be1f7c75317| 2020년 7월 29일<br>2024년 6월 27일| 2f2877c5d778c31e0f29c 7e371df5471bd673173| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.crl)|
|Microsoft Azure TLS Issuing CA 02| 0c6ae97cced59983 8690a00a9ea53214| 2020년 7월 29일<br>2024년 6월 27일| e7eea674ca718e3befd 90858e09f8372ad0ae2aa| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.crl) |
| Microsoft Azure TLS Issuing CA 05| 0d7bede97d8209967a 52631b8bdd18bd| 2020년 7월 29일<br>2024년 6월 27일| 6c3af02e7f269aa73a fd0eff2a88a4a1f04ed1e5| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.crl) |
| Microsoft Azure TLS Issuing CA 06| 02e79171fb8021e93fe 2d983834c50c0| 2020년 7월 29일<br>2024년 6월 27일| 30e01761ab97e59a06b 41ef20af6f2de7ef4f7b0| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.crl) |


 ### <a name="baltimore-cybertrust-root"></a>Baltimore CyberTrust Root

| 루트 CA| 일련 번호| 발급 날짜 만료 날짜| SHA1 지문| URI |
| - | - | - | - | - | 
| Baltimore CyberTrust Root| 020000b9| 2000년 5월 12일<br>2025년 5월 12일| d4de20d05e66fc53fe 1a50882c78db2852cae474|<br>[CDP](http://crl3.digicert.com/Omniroot2025.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>연결된 중간 CA

| 발급 및 중간 CA| 일련 번호| 발급 날짜 만료 날짜| SHA1 지문| URI |
| - | - | - | - | - | 
| Microsoft RSA TLS CA 01| 703d7a8f0ebf55aaa 59f98eaf4a206004eb2516a| 2020년 7월 21일<br>2024년 10월 8일| 417e225037fbfaa4f9 5761d5ae729e1aea7e3a42| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2001.crl)<br>[OCSP](http://ocsp.msocsp.com/) |
| Microsoft RSA TLS CA 02| b0c2d2d13cdd56cdaa 6ab6e2c04440be4a429c75| 2020년 7월 21일<br>2024년 5월 20일| 54d9d20239080c32316ed 9ff980a48988f4adf2d| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2002.crl)<br>[OCSP](http://ocsp.msocsp.com/) |


 ### <a name="digicert-global-root-ca"></a>DigiCert Global Root CA

| 루트 CA| 일련 번호| 발급 날짜 만료 날짜| SHA1 지문| URI |
| - | - | - | - | - | 
| DigiCert Global Root CA| 083be056904246 b1a1756ac95991c74a| 2006년 11월 9일<br>2031년 11월 9일| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>연결된 중간 CA

| 발급 및 중간 CA| 일련 번호| 발급 날짜 만료 날짜| SHA1 지문| URI |
| - | - | - | - | - |
| DigiCert SHA2 Secure Server CA| 01fda3eb6eca75c 888438b724bcfbc91| 2013년 3월 8일, 2023년 3월 8일| 1fb86b1168ec743154062 e8c9cc5b171a4b7ccb4| [AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA.crt)<br>[CDP](http://crl3.digicert.com/ssca-sha2-g6.crl)<br>[OCSP](http://ocsp.digicert.com/) |
| DigiCert SHA2 Secure Server CA |02742eaa17ca8e21 c717bb1ffcfd0ca0 |2020년 9월 22일<br>2030년 9월 22일|626d44e704d1ceabe3bf 0d53397464ac8080142c|[AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA-2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertSHA2SecureServerCA.crl)<br>[OCSP](http://ocsp.digicert.com/)|


## <a name="cas-used-in-azure-china-21vianet-cloud"></a>Azure 중국 21Vianet 클라우드에서 사용되는 CA

### <a name="digicert-global-root-ca"></a>DigiCert Global Root CA


| 루트 CA| 일련 번호| 발급 날짜 만료 날짜| SHA1 지문| URI |
| - | - | - | - | - |
| DigiCert Global Root CA| 083be056904246b 1a1756ac95991c74a| 2006년 11월 9일<br>2031년 11월 9일| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://ocsp.digicert.com/)<br>[OCSP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl) |


#### <a name="associated-intermediate-ca"></a>연결된 중간 CA

| 발급 및 중간 CA| 일련 번호| 발급 날짜 만료 날짜| SHA1 지문| URI |
| - | - | - | - | - | - |
| DigiCert Basic RSA CN CA G2| 02f7e1f982bad 009aff47dc95741b2f6| 2020년 3월 4일<br>2030년 3월 4일| 4d1fa5d1fb1ac3917c08e 43f65015e6aea571179| [AIA](http://cacerts.digicert.cn/DigiCertBasicRSACNCAG2.crt)<br>[CDP](http://crl.digicert.cn/DigiCertBasicRSACNCAG2.crl)<br>[OCSP](http://ocsp.digicert.cn/) |

## <a name="next-steps"></a>다음 단계
[Microsoft 365 암호화 체인에 대해 알아보기](/microsoft-365/compliance/encryption-office-365-certificate-chains)
