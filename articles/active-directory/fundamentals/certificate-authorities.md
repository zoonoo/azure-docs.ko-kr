---
title: Azure Active Directory 인증 기관
description: Azure에서 사용 되는 신뢰할 수 있는 인증서 목록
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
ms.openlocfilehash: b0ab8eac97a6b02377d38653a990a2f0d5ff81ba
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94334961"
---
# <a name="certificate-authorities-used-by-azure-active-directory"></a>Azure Active Directory에서 사용 하는 인증 기관

> [!IMPORTANT]
> 이 페이지의 정보는 허용 되는 Ca (인증 기관) 목록을 명시적으로 지정 하는 엔터티와 관련이 있습니다. 인증서 고정 이라고 하는이 방법은 다른 옵션이 없는 경우에는 피해 야 합니다.

TLS/SSL 프로토콜을 통해 Azure Active Directory (Azure AD) id 서비스에 액세스 하려는 모든 엔터티는 아래 나열 된 Ca의 인증서를 제공 합니다. 엔터티가 이러한 Ca를 신뢰 하는 경우 인증서를 사용 하 여 id 서비스의 id 및 적법성을 확인 하 고 보안 연결을 설정할 수 있습니다.

인증 기관은 루트 Ca 및 중간 Ca로 분류할 수 있습니다. 일반적으로 루트 Ca에는 하나 이상의 중간 Ca가 연결 되어 있습니다. 이 문서에는 Azure AD id 서비스에서 사용 하는 루트 Ca와 이러한 각 루트와 연결 된 중간 Ca가 나열 됩니다. 각 CA에 대 한 Uri (Uniform Resource Identifier)를 포함 하 여 연결 된 AIA (기관 정보 액세스) 및 인증서 해지 목록 배포 지점 (CDP) 파일을 다운로드 합니다. 해당 하는 경우에는 OCSP (온라인 인증서 상태 프로토콜) 끝점에 대 한 URI도 제공 합니다.

## <a name="cas-used-in-azure-public-and-azure-us-government-clouds"></a>Azure 공용 및 Azure 미국 정부 클라우드에서 사용 되는 Ca

서비스 마다 다른 루트 또는 중간 Ca를 사용할 수 있습니다. 따라서 아래 나열 된 모든 항목이 필요할 수 있습니다.

### <a name="digicert-global-root-g2"></a>DigiCert Global Root G2


| 루트 CA| 일련 번호| 발급 날짜 만료 날짜| SHA1 지문| URI |
| - |- |-|-|-|-|
| DigiCert Global Root G2| 033af1e6a711a 9a0bb2864b11d09fae5| 2013년 8월 1일 <br>2038 년 1 월 15 일| df3c24f9bfd666761b268 073fe06d1cc8d4f82a4| [AIA](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertGlobalRootG2.crl) |


#### <a name="associated-intermediate-cas"></a>연결 된 중간 Ca

| 발급 및 중간 CA| 일련 번호| 발급 날짜 만료 날짜| SHA1 지문| URI |
| - | - | - | - | - | 
| TLS 발급 CA 01 Microsoft Azure| 0aafa6c5ca63c45141 ea3be1f7c75317| 2020 년 7 월 29 일<br>2024 년 6 월 27 일| 2f2877c5d778c31e0f29c 7e371df5471bd673173| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.crl)|
|Microsoft Azure TLS 발급 CA 02| 0c6ae97cced59983 8690a00a9ea53214| 2020 년 7 월 29 일<br>2024 년 6 월 27 일| e7eea674ca718e3befd 90858e09f8372ad0ae2aa| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.crl) |
| Microsoft Azure TLS 발급 CA 05| 0d7bede97d8209967a 52631b8bdd18bd| 2020 년 7 월 29 일<br>2024 년 6 월 27 일| 6c3af02e7f269aa73a fd0eff2a88a4a1f04ed1e5| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.crl) |
| Microsoft Azure TLS 발급 CA 06| 02e79171fb8021e93fe 2d983834c50c0| 2020 년 7 월 29 일<br>2024 년 6 월 27 일| 30e01761ab97e59a06b 41ef20af6f2de7ef4f7b0| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.crl) |


 ### <a name="baltimore-cybertrust-root"></a>Baltimore CyberTrust Root

| 루트 CA| 일련 번호| 발급 날짜 만료 날짜| SHA1 지문| URI |
| - | - | - | - | - | 
| Baltimore CyberTrust Root| 020000b9| 2000 년 5 월 12 일<br>2025 년 5 월 12 일| d4de20d05e66fc53fe 1a50882c78db2852cae474|<br>[CDP](http://crl3.digicert.com/Omniroot2025.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>연결 된 중간 Ca

| 발급 및 중간 CA| 일련 번호| 발급 날짜 만료 날짜| SHA1 지문| URI |
| - | - | - | - | - | 
| Microsoft RSA TLS CA 01| 703d7a8f0ebf55aaa 59f98eaf4a206004eb2516a| 2020년 7월 21일<br>2024 년 10 월 8 일| 417e225037fbfaa4f9 5761d5ae729e1aea7e3a42| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2001.crl)<br>[OCSP](http://ocsp.msocsp.com/) |
| Microsoft RSA TLS CA 02| b0c2d2d13cdd56cdaa 6ab6e2c04440be4a429c75| 2020년 7월 21일<br>5 월 20 일, 2024| 54d9d20239080c32316ed 9ff980a48988f4adf2d| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2002.crl)<br>[OCSP](http://ocsp.msocsp.com/) |


 ### <a name="digicert-global-root-ca"></a>DigiCert Global Root CA

| 루트 CA| 일련 번호| 발급 날짜 만료 날짜| SHA1 지문| URI |
| - | - | - | - | - | 
| DigiCert Global Root CA| 083be056904246 b1a1756ac95991c74a| 2006 년 11 월 9 일<br>2031 년 11 월 9 일| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>연결 된 중간 Ca

| 발급 및 중간 CA| 일련 번호| 발급 날짜 만료 날짜| SHA1 지문| URI |
| - | - | - | - | - |
| DigiCert SHA2 Secure Server CA| 01fda3eb6eca75c 888438b724bcfbc91| 2013 년 3 월 8 일, 2023 년 3 월 8 일| 1fb86b1168ec743154062 e8c9cc5b171a4b7ccb4| [AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA.crt)<br>[CDP](http://crl3.digicert.com/ssca-sha2-g6.crl)<br>[OCSP](http://ocsp.digicert.com/) |
| DigiCert SHA2 Secure Server CA |02742eaa17ca8e21 c717bb1ffcfd0ca0 |2020년 9월 22일<br>2030 년 9 월 22 일|626d44e704d1ceabe3bf 0d53397464ac8080142c|[AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA-2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertSHA2SecureServerCA.crl)<br>[OCSP](http://ocsp.digicert.com/)|


## <a name="cas-used-in-azure-china-21vianet-cloud"></a>Azure 중국 21Vianet 클라우드에서 사용 되는 Ca

### <a name="digicert-global-root-ca"></a>DigiCert Global Root CA


| 루트 CA| 일련 번호| 발급 날짜 만료 날짜| SHA1 지문| URI |
| - | - | - | - | - |
| DigiCert Global Root CA| 083be056904246b 1a1756ac95991c74a| 11 월 9 일, 2006<br>11 월 9 일, 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://ocsp.digicert.com/)<br>[OCSP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl) |


#### <a name="associated-intermediate-ca"></a>연결 된 중간 CA

| 발급 및 중간 CA| 일련 번호| 발급 날짜 만료 날짜| SHA1 지문| URI |
| - | - | - | - | - | - |
| DigiCert 기본 RSA CN CA G2| 02f7e1f982bad 009aff47dc95741b2f6| 2020 년 3 월 4 일<br>2030 년 3 월 4 일| 4d1fa5d1fb1ac3917c08e 43f65015e6aea571179| [AIA](http://cacerts.digicert.cn/DigiCertBasicRSACNCAG2.crt)<br>[CDP](http://crl.digicert.cn/DigiCertBasicRSACNCAG2.crl)<br>[OCSP](http://ocsp.digicert.cn/) |

## <a name="next-steps"></a>다음 단계
[Microsoft 365 암호화 체인에 대해 알아보기](https://docs.microsoft.com/microsoft-365/compliance/encryption-office-365-certificate-chains?view=o365-worldwide)
