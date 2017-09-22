---
title: "Azure에서 암호화를 사용하여 전송 중인 개인 데이터 보호 | Microsoft Docs"
description: "Azure에서 암호화를 사용하여 개인 데이터를 보호합니다."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 8c59fa7e9315922af4fab5f8492194b50459ef8b
ms.contentlocale: ko-kr
ms.lasthandoff: 09/20/2017

---
# <a name="azure-encryption-technologies-protect-personal-data-in-transit-with-encryption"></a>Azure 암호화 기술: 암호화를 사용하여 전송 중인 개인 데이터 보호

이 문서는 Azure 암호화 기술을 이해하고 사용하여 전송 중인 데이터를 보호하는 데 도움이 됩니다. 

네트워크를 통해 이동하는 개인 데이터의 개인 정보를 보호하는 것은 다계층 심층 방어 보안 전략의 필수적인 부분입니다. 전송 중 암호화는 전송을 가로채는 공격자가 데이터를 보거나 사용할 수 없도록 설계되었습니다.

## <a name="scenario"></a>시나리오

미국에 본사를 둔 대형 크루즈 회사는 영국 제도뿐만 아니라 지중해, 아드리아해 및 발트해의 여정을 제공하기 위해 운영을 확대하고 있습니다. 이러한 노력을 지원하기 위해 이탈리아, 독일, 덴마크 및 영국에 기반을 둔 몇 개의 소형 크루즈 라인을 인수했습니다. 

회사에서 Microsoft Azure를 사용하여 클라우드에 회사 데이터를 저장합니다. 여기에는 전 세계 고객 기반의 이름, 주소, 전화 번호 및 신용 카드 정보와 같이 식별 가능한 개인 정보가 포함됩니다. 주소, 전화 번호, 납세자 번호 및 모든 위치의 회사 직원에 대한 기타 정보와 같이 기존의 인적 자원 정보도 포함됩니다. 또한 크루즈 라인에서 현재 및 과거 고객과의 관계를 추적하기 위해 개인 정보가 포함된 보상 및 충성도 프로그램 구성원에 대한 대규모 데이터베이스를 유지하고 있습니다.

고객의 개인 데이터는 회사의 원격 사무실 및 전 세계에 위치한 여행사로부터 데이터베이스에 입력됩니다. 고객 정보가 포함된 문서는 네트워크를 통해 Azure 저장소로 전송됩니다.

## <a name="problem-statement"></a>문제 설명

Azure 서비스에서 전송되는 동안 고객 및 직원 개인 데이터의 개인 정보는 회사에서 보호해야 합니다.

## <a name="company-goal"></a>회사 목표

디스크 외부에 있는 개인 데이터를 암호화하는 것이 회사의 목표입니다. 권한 없는 사람이 디스크 외부의 개인 데이터를 가로채는 경우 읽을 수 없는 형식으로 렌더링해야 합니다. 암호화를 적용하는 경우 사용자 또는 관리자에게 쉽고 완전히 투명해야 합니다.

## <a name="solutions"></a>솔루션

Azure 서비스는 전송 중인 개인 데이터를 보호할 수 있는 여러 도구와 기술을 제공합니다.

### <a name="azure-storage"></a>Azure 저장소

클라우드에 저장되는 데이터는 전 세계 어디서나 물리적으로 위치할 수 있는 클라이언트에서 Azure 데이터 센터로 이동해야 합니다. 사용자가 해당 데이터를 검색하는 경우 반대 방향으로 다시 이동합니다. 공용 인터넷을 통해 전송되는 데이터는 항상 공격자가 가로챌 위험이 있습니다. 이에 따라 전송 수준 암호화를 사용하여 위치 간에 이동할 때 개인 데이터의 개인 정보를 보호해야 합니다.

HTTPS 프로토콜은 인터넷을 통해 암호화된 보안 통신 채널을 제공합니다. Azure Storage의 개체에 액세스하고 REST API를 호출할 때에는 HTTPS를 사용해야 합니다. [SAS(공유 액세스 서명)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1)를 사용하여 Azure Storage 개체에 대한 액세스 권한을 위임하는 경우 HTTPS 프로토콜을 사용하도록 강제 적용합니다. SAS에는 두 가지 유형, 즉 서비스 SAS와 계정 SAS가 있습니다.

#### <a name="how-do-i-construct-a-service-sas"></a>서비스 SAS를 구성하려면 어떻게 할까요?

서비스 SAS는 저장소 서비스(Blob, 큐, 테이블, 파일 서비스) 중 하나의 리소스에 대한 액세스 권한을 위임합니다. 서비스 SAS를 구성하려면 다음을 수행합니다.

1. 서명된 버전 필드를 지정합니다.

2. 서명된 리소스를 지정합니다(Blob Service 및 File Service만 해당).

3. 응답 헤더를 재정의하는 쿼리 매개 변수를 지정합니다(Blob Service 및 File Service만 해당).

4. 테이블 이름을 지정합니다(Table Service만 해당).

5. 액세스 정책을 지정합니다.

6. 서명 유효성 검사 간격을 지정합니다.

8. 권한을 지정합니다.

9. IP 주소 또는 IP 범위를 지정합니다.

10. HTTP 프로토콜을 지정합니다.

11. 테이블 액세스 범위를 지정합니다.

12. 서명된 식별자를 지정합니다.

13. 서명을 지정합니다.

자세한 지침은 [서비스 SAS 구성](https://docs.microsoft.com/rest/api/storageservices/Constructing-a-Service-SAS?redirectedfrom=MSDN)을 참조하세요.

#### <a name="how-do-i-construct-an-account-sas"></a>계정 SAS를 구성하려면 어떻게 할까요?

계정 SAS는 하나 이상의 저장소 서비스에 있는 리소스에 대한 액세스 권한을 위임합니다. 또한 서비스 SAS로 허용되지 않는 Blob 컨테이너, 테이블, 큐, 파일 공유에서 읽기, 쓰기, 삭제 작업에 대한 액세스 권한을 위임할 수 있습니다. 계정 SAS의 구성은 서비스 SAS의 구성과 비슷합니다. 자세한 지침은 [계정 SAS 구성](https://docs.microsoft.com/rest/api/storageservices/Constructing-an-Account-SAS?redirectedfrom=MSDN)을 참조하세요.

#### <a name="how-do-i-enforce-https-when-calling-rest-apis"></a>REST API를 호출할 때 HTTPS를 적용하려면 어떻게 할까요?

저장소 계정의 개체에 액세스하기 위해 REST API를 호출할 때 HTTPS를 사용하도록 적용하려면 저장소 계정에 대해 [보안 전송 필요]를 사용하도록 설정할 수 있습니다. 

1. Azure Portal에서 **저장소 계정 만들기**를 선택하거나 기존 저장소 계정의 경우 **설정**, **구성**을 차례로 선택합니다.

2. **보안 전송 필요** 아래에서 **사용**을 선택합니다.

![저장소 계정 만들기](media/protect-personal-data-in-transit-encryption/create-storage-account.png)

프로그래밍 방식으로 [보안 전송 필요]를 사용하도록 설정하는 방법을 포함한 자세한 지침은 [보안 전송 필요](https://docs.microsoft.com/azure/storage/storage-require-secure-transfer)를 참조하세요.

#### <a name="how-do-i-encrypt-data-in-azure-file-storage"></a>Azure File Storage에서 데이터를 암호화하려면 어떻게 할까요?

[Azure File Storage](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-portal)를 사용하여 전송 중인 데이터를 암호화하려면 Windows 8, 8.1 및 10, Windows Server 2012 R2 및 Windows Server 2016에서 SMB 3.x를 사용할 수 있습니다. Azure 파일 서비스를 사용하는 경우 "보안 전송 필요"가 설정된 경우 암호화되지 않은 모든 연결이 실패합니다. 여기에는 암호화되지 않은 SMB 2.1, SMB 3.0을 사용하는 시나리오와 Linux SMB 클라이언트의 일부 유형이 포함됩니다.

#### <a name="azure-client-side-encryption"></a>Azure 클라이언트 쪽 암호화

클라이언트 응용 프로그램과 Azure Storage 간에 전송되는 데이터를 보호하기 위한 또 다른 옵션은 [클라이언트 쪽 암호화](https://docs.microsoft.com/azure/storage/storage-client-side-encryption)입니다. Azure Storage로 전송되기 전에 데이터가 암호화되며, Azure Storage에서 데이터를 검색할 때 클라이언트 쪽에서 받은 후에 데이터가 암호 해독됩니다.

### <a name="azure-site-to-site-vpn"></a>Azure 사이트 간 VPN

회사 네트워크 또는 사용자와 Azure 가상 네트워크 간에 전송 중인 개인 데이터를 보호하는 효과적인 방법은 [사이트 간](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) 또는 [지점 및 사이트 간](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) VPN(가상 사설망)을 사용하는 것입니다. VPN 연결은 인터넷을 통해 암호화된 보안 터널을 만듭니다.

#### <a name="how-do-i-create-a-site-to-site-vpn-connection"></a>사이트 간 VPN 연결을 만들려면 어떻게 할까요?

사이트 간 VPN은 회사 네트워크의 여러 사용자를 Azure에 연결합니다. Azure Portal에서 사이트 간 연결을 만들려면 다음을 수행합니다.

1. 가상 네트워크를 만듭니다.

2. DNS 서버를 지정합니다.

3. 게이트웨이 서브넷을 만듭니다.

4. VPN 게이트웨이를 만듭니다. 

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-01.png)

5. 로컬 네트워크 게이트웨이를 만듭니다.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-02.png)

6. VPN 장치를 구성합니다.

7. VPN 연결을 만듭니다.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-03.png)

8. VPN 연결을 확인합니다.

Azure Portal에서 사이트 간 연결을 만드는 방법에 대한 자세한 내용은 [Azure Portal에서 사이트 간 연결 만들기](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)를 참조하세요.

#### <a name="how-do-i-create-a-point-to-site-vpn-connection"></a>지점 및 사이트 간 VPN 연결을 만들려면 어떻게 할까요?

지점 및 사이트 간 VPN은 개별 클라이언트 컴퓨터에서 가상 네트워크로의 보안 연결을 만듭니다. 이는 집이나 호텔 또는 회의 센터와 같은 원격 위치에서 Azure에 연결하려는 경우에 유용합니다. Azure Portal에서 지점 및 사이트 간 연결을 만들려면 다음을 수행합니다.

1. 가상 네트워크를 만듭니다.

2. 게이트웨이 서브넷을 추가합니다.

3. DNS 서버를 지정합니다. (선택 사항)

4. 가상 네트워크 게이트웨이를 만듭니다.

5. 인증서를 생성합니다.

6. 클라이언트 주소 풀을 추가합니다.

7. 루트 인증서 공용 인증서 데이터를 업로드합니다.

8. VPN 클라이언트 구성 패키지를 생성 및 설치합니다.

9. 내보낸 클라이언트 인증서를 설치합니다.

10. Azure에 연결

11. 연결을 확인합니다.

자세한 지침은 [인증서 인증을 사용하여 VNet에 지점 및 사이트 간 연결 구성: Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)을 참조하세요.

### <a name="ssltls"></a>SSL/TLS

항상 SSL/TLS 프로토콜을 사용하여 서로 다른 위치에서 데이터를 교환하는 것이 좋습니다. [ExpressRoute](https://docs.microsoft.com/azure/expressroute/)를 사용하여 전용 고속 WAN 링크를 통해 대용량 데이터 집합을 이동하도록 선택한 조직에서는 보호를 강화하기 위해 SSL/TLS 또는 다른 프로토콜을 사용하여 응용 프로그램 수준에서 데이터를 암호화할 수도 있습니다.

### <a name="encryption-by-default"></a>기본적으로 암호화

Microsoft는 암호화를 사용하여 고객과 Azure 클라우드 서비스 간에 전송 중인 데이터를 보호합니다. Azure 포털을 통해 Azure 저장소와 상호 작용하는 경우 모든 트랜잭션이 HTTPS를 통해 발생합니다.

[TLS(전송 계층 보안)](https://en.wikipedia.org/wiki/Transport_Layer_Security)는 Microsoft 데이터 센터에서 Microsoft 클라우드 서비스에 연결하는 클라이언트 시스템과 협상하려고 하는 프로토콜입니다. TLS는 강력한 인증, 메시지 개인 정보 보호 및 무결성(메시지 변조, 가로채기 및 위조에 대한 검색 가능), 상호 운용성, 알고리즘 유연성, 배포 및 사용 편의성을 제공합니다.

또한 고객의 클라이언트 시스템과 Microsoft의 클라우드 서비스 간의 각 연결에서 고유한 키를 사용하도록 [PFS(Perfect Forward Secrecy)](https://en.wikipedia.org/wiki/Forward_secrecy)도 채택되었습니다. Microsoft 클라우드 서비스에 연결하면 RSA 기반 2,048비트 암호화 키 길이를 활용할 수도 있습니다. TLS, RSA 2,048비트 키 길이 및 PFS를 조합하여 사용하면 다른 사용자가 Microsoft 클라우드 서비스와 고객 간에 전송되는 데이터를 가로채어 액세스하기가 훨씬 더 어려워집니다.

전송 중인 데이터는 항상 [Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview)에서 암호화됩니다. 영구 미디어에 저장하기 전에 데이터를 암호화하는 것 외에도 HTTPS를 사용하여 전송 중 데이터를 항상 안전하게 보호합니다. HTTPS는 Data Lake Store REST 인터페이스에 지원되는 유일한 프로토콜입니다.

## <a name="summary"></a>요약

회사에서 Azure Storage에 HTTPS 연결을 강제 적용하고, 공유 액세스 서명을 사용하고, 저장소 계정에서 [보안 전송 필요]를 사용하도록 설정하여 개인 데이터 및 이러한 데이터의 개인 정보를 보호하는 목표를 달성할 수 있습니다. 또한 SMB 3.0 연결을 사용하고 클라이언트 쪽 암호화를 구현하여 개인 데이터를 보호할 수도 있습니다. 회사 네트워크에서 Azure 가상 네트워크로의 사이트 간 VPN 연결과 개별 사용자의 지점 및 사이트 간 VPN 연결은 개인 데이터가 안전하게 이동할 수 있는 보안 터널을 만듭니다. Microsoft의 기본 암호화 사례는 개인 데이터의 개인 정보 보호를 더욱 강화합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 데이터 보안 및 암호화 모범 사례](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices)

- [VPN Gateway 계획 및 설계](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

- [VPN 게이트웨이 FAQ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq)

- [Azure App Service에 대한 SSL 인증서 구입 및 구성](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)

