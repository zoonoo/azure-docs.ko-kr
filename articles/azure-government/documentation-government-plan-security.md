---
title: "Azure Government 보안 | Microsoft 문서"
description: "Azure Government에 제공되는 서비스의 개요 설명"
services: Azure-Government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
editor: 
ms.assetid: c3645bda-bf35-4232-a54d-7a0bfab2d594
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/18/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: a9cc2bc044caddec63645932067f7a346945db78
ms.openlocfilehash: 11d0a67a4c368b50e2f2e8648c4fb2db8e0421c2


---
# <a name="security"></a>보안
## <a name="principles-for-securing-customer-data-in-azure-government"></a>Azure Government에서 고객 데이터를 보호하기 위한 원칙
Azure Government는 규제된/제어된 데이터 요구 사항에 맞게 클라우드 솔루션을 구축하는 데 사용할 수 있는 다양한 기능 및 서비스를 제공합니다. 규정 준수 고객 솔루션은 견고한 데이터 보안 사례로 결합된 기본 제공 Azure Government 기능의 효과적인 구현일 뿐입니다.

Azure Government에서 솔루션을 호스팅하는 경우 Microsoft는 클라우드 인프라 수준에서 이러한 요구 사항 중 대부분을 처리합니다.

다음 다이어그램에서는 Azure 심층 방어 모델을 보여 줍니다. 예를 들어 Microsoft는 고객 관련 응용 프로그램 DDOS 필요에 대한 보안 어플라이언스와 같은 고객 기능과 함께 기본 클라우드 인프라 DDOS를 제공합니다.

![대체 텍스트](./media/azure-government-Defenseindepth.png)

이 페이지에서는 이러한 원칙을 적용하는 방법에 대한 지침 및 모범 사례 즉, ITAR 정보를 처리하는 솔루션에 필요한 의무 및 책임을 충족하기 위해 Azure Government를 효과적으로 사용하는 방법을 제공하여 서비스 및 응용 프로그램을 보호하기 위한 기본 원칙을 간략하게 설명합니다.

 고객 데이터를 보호하기 위한 포괄적인 원칙은 다음과 같습니다.

* 암호화를 사용하여 데이터 보호
* 암호 관리
* 데이터 액세스를 제한하도록 격리

### <a name="protecting-customer-data-using-encryption"></a>암호화를 사용하여 고객 데이터 보호
위험을 완화하고 규정 의무를 충족하는 것은 데이터 암호화에 대한 포커스 및 중요성의 증가를 가져옵니다. 효과적인 암호화 구현을 사용하여 현재 네트워크 및 응용 프로그램 보안 조치를 강화하고 클라우드 환경의 전체적인 위험을 감소시킵니다.

#### <a name="encryption-at-rest"></a>휴지 상태의 암호화
미사용 데이터 암호화는 디스크 저장소에 보관된 고객 콘텐츠 보호에 적용됩니다. 이는 여러 가지 방법으로 발생할 수 있습니다.

#### <a name="storage-service-encryption"></a>저장소 서비스 암호화
Azure 저장소 서비스 암호화는 저장소 계정 수준에 사용하도록 설정되고 이로 인해 블록 Blob 및 페이지 Blob이 Azure 저장소에 쓸 때 자동으로 암호화됩니다. Azure 저장소에서 데이터를 읽을 때 데이터가 반환되기 전에 저장소 서비스에서 해당 암호가 해독됩니다. 이를 사용하여 코드를 수정하거나 응용 프로그램에 추가하지 않고도 데이터를 보호합니다.

#### <a name="client-side-encryption"></a>클라이언트 쪽 암호화
클라이언트 쪽 암호화가 Java 및 .NET 저장소 클라이언트 라이브러리에 기본적으로 제공되어 있으며, 여기에서 Azure 키 자격 증명 모음 API를 활용할 수 있으므로 구현하기가 단순합니다. Azure Active Directory를 사용하여 특정 개인에 대해 Azure 주요 자격 증명 모음의 암호에 대한 액세스 권한을 부여하도록 Azure 주요 자격 증명 모음을 사용합니다.

#### <a name="encryption-in-transit"></a>전송 중 암호화
Azure Government에 대한 연결에 사용할 수 있는 기본 암호화는 TLS(전송 수준 보안) 1.2 프로토콜 및 X.509 인증서를 지원합니다. FIPS(Federal Information Processing Standard) 140-2 수준 1 암호화 알고리즘은 Azure Government 데이터 센터 간의 인프라 네트워크 연결에도 사용됩니다.  Windows Server 2012 R2 및 Windows 8-plus VM 및 Azure 파일 공유는 VM과 파일 공유 간의 암호화에 SMB 3.0을 사용할 수 있습니다. 클라이언트 쪽 암호화를 사용하여 클라이언트 응용 프로그램의 저장소로 전송되기 전에 데이터를 암호화하고 저장소 외부로 전송된 후에 데이터의 암호를 해독합니다.

#### <a name="best-practices-for-encryption"></a>암호화에 대한 모범 사례
* IaaS VM: Azure Disk Encryption을 사용합니다. 저장소 서비스 암호화를 켜서 Azure 저장소에 해당 디스크를 백업하는 데 사용되는 VHD 파일을 암호화하지만 새로 작성된 데이터만 암호화됩니다. 즉, VM을 만든 다음 VHD 파일을 보유하는 저장소 계정에 대해 저장소 서비스 암호화를 사용하도록 설정하면 원래 VHD 파일이 아닌 변경 내용만 암호화됩니다.
* 클라이언트 쪽 암호화: 이는 전송 전에 데이터를 암호화하고 미사용 데이터를 암호화하기 때문에 가장 안전한 데이터 암호화 방법입니다. 그러나 원치 않게, 저장소를 사용하여 응용 프로그램에 코드를 추가해야 합니다. 이러한 경우 전송 중인 데이터에 대해서는 HTTP를 사용하고 미사용 데이터의 암호화를 위해서는 저장소 서비스 암호화를 사용할 수 있습니다. 클라이언트 쪽 암호화는 클라이언트에서 더 많은 부하를 발생하므로 확장성 계획을 세울 때, 특히 대량의 데이터를 암호화하고 전송하는 경우에 이러한 사실을 고려해야 합니다.

### <a name="protecting-customer-data-by-managing-secrets"></a>암호를 관리하여 고객 데이터 보호
안전한 키 관리는 클라우드에서 데이터를 보호하기 위해 아주 중요합니다. 고객은 키 관리를 간소화하고 데이터를 암호화하기 위해 클라우드 응용 프로그램 및 서비스에서 사용되는 키의 제어를 유지 관리하기 위해 노력해야 합니다.

#### <a name="best-practices-for-managing-secrets"></a>암호 관리 모범 사례
* 주요 자격 증명 모음을 사용하여 하드 코드된 구성 파일, 스크립트 또는 소스 코드를 통해 노출되는 암호의 위험을 최소화합니다. Azure 주요 자격 증명 모음은 키(예: Azure Disk Encryption에 대한 암호화 키) 및 암호(예: 비밀번호)를 FIPS 140-2 Level 2 유효성 검사된 하드웨어 보안 모듈(HSM)에 저장하여 암호화합니다. 추가된 보증을 위해 해당 HSM에서 키를 생성하거나 가져올 수 있습니다.
* 응용 프로그램 코드 및 템플릿만 암호에 대한 URI 참조를 포함해야 하므로 코드, 구성 또는 소스 코드 리포지토리에는 실제 암호가 없게 됩니다. 따라서 GitHub의 harvest-bots 등과 같은 내부 또는 외부 리포지토리에 대한 키 피싱 공격을 차단할 수 있습니다.
* 주요 자격 증명 모음 내에서 강력한 RBAC 제어를 사용합니다. 신뢰할 수 있는 직원이 퇴사하거나 회사 내의 새 그룹으로 이동하는 경우 암호에 액세스할 수 없도록 방지해야 합니다.

자세한 내용은 [Azure Key Vault 공개 문서](../key-vault/index.md)를 참조하세요.

### <a name="isolation-to-restrict-data-access"></a>데이터 액세스를 제한하도록 격리
격리는 인증된 사용자, 서비스 및 응용 프로그램에 대해서만 데이터 액세스를 제한하도록 경계, 구분 및 컨테이너를 사용하는 것입니다. 예를 들어 테넌트 간의 구분은 Microsoft Azure와 같은 다중 테넌트 클라우드 플랫폼에 중요한 보안 메커니즘입니다. 논리적 격리는 하나의 테넌트가 다른 테넌트의 작업을 방해하지 않도록 방지하는 데 도움을 줍니다.

#### <a name="environment-isolation"></a>환경 격리
Azure Government 환경은 Microsoft 네트워크의 나머지 부분과 분리된 물리적 인스턴스입니다. 격리는 다음을 포함하는 일련의 물리적 및 논리적 제어를 통해 수행됩니다.

* 생체 인식 장치 및 카메라를 사용하여 물리적 장벽 보안.
* 특정 자격 증명 및 프로덕션 환경에 대한 논리적 액세스가 필요한 Microsoft 담당자에 의한 다단계 인증의 사용.
* Azure Government에 대한 모든 서비스 인프라는 미국 내에 위치합니다.

#### <a name="per-customer-isolation"></a>고객별 격리
Azure는 VLAN 격리, ACL, 부하 분산 장치 및 IP 필터를 통해 네트워크 액세스 제어 및 분리를 구현합니다.

고객은 구독, 리소스 그룹, 가상 네트워크 및 서브넷 간에 리소스를 추가적으로 격리할 수 있습니다.

## <a name="screening"></a>검사
최근에 발표된 FedRAMP High 및 국방부(DoD) Impact Level 4 승인. 이로 인해 Azure Government 환경의 보안 및 규정 준수 수준이 높아졌습니다.

이제는 DoD 클라우드 컴퓨팅 SRG(보안 요구 사항 가이드)의 5.6.2.2 조항에 정의된 대로 NACLC(National Agency Check with Law and Credit)에서 모든 운영자를 검사합니다.

> [!NOTE]
> “비밀 취급 인가”(예: DoD의 ADP-2)에 따라 레벨 4 및 5 정보에 액세스할 수 있는 CSP 인원에 필요한 최소 배경 조사는 NACLC(National Agency Check with Law and Credit)(“비밀 취급 인가” 계약자에 대한) 또는 “보통 위험” 직위 지정에 대한 MBI(Moderate Risk Background Investigation)입니다.
> 
> 

다음 테이블에는 Azure Government 운영자에 대한 현재 검사 기준이 요약되어 있습니다.

| Azure Government 검사 및 배경 확인 | 설명 |
| --- | --- |
| 미국 시민권 |미국 시민권 확인 |
| Microsoft 클라우드 배경 확인(2년마다) |사회 보장 번호 조회, 범죄 기록 검사, 해외 자산 통제실(OFAC) 목록, 산업안보국(BIS) 목록, 국방무역통제 사무소 금지 인물 목록. |
| NACLC(National Agency Check with Law and Credit)(5년마다) |FBI 데이터베이스에 대한 지문 배경 확인 추가. 자세한 내용은<a href="https://www.opm.gov/investigations/background-investigations/federal-investigations-notices/1997/fin97-02/"> 인사관리처 사이트</a>를 방문하세요. |
| <a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/CJIS"> CJIS(Criminal Justice Information Services) </a> |CJIS는 CJI(Criminal Justice Information) 데이터에 대한 액세스 권한이 부여될 수 있는 운영 직원의 지문 기록을 처리하고 범죄 기록을 확인하는 주 정부, 지방 정부 및 FBI 정부 검사입니다.  각 주는 고유의 방법에 따라 CJI에 대한 액세스 권한이 부여될 가능성이 있는 모든 직원의 배경을 조사하고 후속 승인을 처리합니다. |

Azure 운영 직원의 경우 다음 액세스 원칙이 적용됩니다.

* 의무가 명확하게 정의되며 변경 내용을 요청, 승인, 배포할 별도의 책임이 있습니다.
* 액세스는 특정 기능이 있는 정의된 인터페이스를 통해 이루어집니다.
* 액세스는 JIT(just-in-time)이며, 사건별로 또는 특정 유지 관리 이벤트별로 부여되고 항상 제한 시간이 있습니다.
* 액세스는 규칙 기반이며, 문제 해결에 필요한 권한만 할당된 역할이 정의됩니다.

검사 표준에는 Azure Government가 호스트하는 시스템에 대한 액세스 권한이 부여되기 이전부터 근무한 모든 Microsoft 고객 지원 및 운영 직원의 미국 시민권 검사가 포함됩니다. 데이터를 전송해야 하는 고객 지원 담당자는 Azure Government 내에서 보안 기능을 사용합니다. 보안 데이터를 전송하려면 별도의 인증 자격 증명으로 액세스 권한을 얻어야 합니다. 예를 들어 시스템 메타데이터에 액세스하기 위해 운영 담당자는 특정 웹 기반 내부 관리 도구, 읽기 전용 API 및 JIT 권한 상승을 사용합니다.

## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트는 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.




<!--HONumber=Nov16_HO3-->


