---
title: Oracle 클라우드 인프라를 사용 하 여 Microsoft Azure 통합 | Microsoft Docs
description: 데이터베이스에 클라우드 인프라 OCI (Oracle)를 사용 하 여 Microsoft Azure에서 실행 되는 Oracle 응용 프로그램을 통합 하는 솔루션에 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 711fce9627537e68171c3b170121900d6b14ca1e
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743652"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Microsoft Azure 및 Oracle 클라우드 인프라 (미리 보기)를 통합 하는 oracle 응용 프로그램 솔루션

Microsoft와 Oracle 관계를 맺고 짧은 대기 시간, 처리량이 높은 클라우드 간 연결을 위해 두 클라우드 모두의 기능을 활용할 수 있습니다. 

이 클라우드 간 연결을 사용 하 여, 데이터베이스 계층에서 클라우드 인프라 OCI (Oracle)를 실행 하는 다중 계층 응용 프로그램 및 응용 프로그램 및 Microsoft Azure에서 다른 계층을 분할할 수 있습니다. 환경 전체 솔루션 스택에서 단일 클라우드에서 실행 하는 것과 비슷합니다. 

> [!IMPORTANT]
> 이 클라우드 간 기능은 현재 미리 보기로 제공에서 하 고 일부 [제한 사항 적용](#preview-limitations)합니다. [부속 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

Azure 인프라에서 완전히 Oracle 솔루션을 배포 하려는 경우 참조 [Oracle VM 이미지 및 Microsoft Azure에서 배포가](oracle-vm-solutions.md)합니다.

## <a name="scenario-overview"></a>시나리오 개요

클라우드 간 연결 OCI에서 호스팅되는 데이터베이스 서비스의 혜택을 누리 하는 동안 Azure virtual machines에서 Oracle의 업계 선도적인 응용 프로그램 및 사용자 고유의 사용자 지정 응용 프로그램을 실행 하는 솔루션을 제공 합니다. 

클라우드 간 구성에서 실행할 수 있습니다 하는 응용 프로그램에는 다음이 포함 됩니다.

* E-business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle 소매 응용 프로그램
* Oracle Hyperion 재무 관리

다음 다이어그램은 연결 된 솔루션의 대략적인 개요입니다. 간단히 하기 위해 다이어그램에는 응용 프로그램 계층과 데이터 계층 보여 줍니다. 응용 프로그램 architecutre에 따라 솔루션이 Azure에서 웹 계층 등 추가 계층을 포함할 수 있습니다. 자세한 내용은 다음 섹션을 참조하세요.

![Azure OCI 솔루션 개요](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>미리 보기 제한 사항

* 미리 보기에서 클라우드 간 연결이 애 슈 번 OCI (미국-애 슈 번-1) 지역 및 Azure 미국 동부 (eastus) 영역에 제한 됩니다.

## <a name="networking"></a>네트워킹

기업 고객 종종 다양화 다양 한 비즈니스 및 운영 상의 여러 클라우드를 통해 워크 로드 배포를 선택 합니다. 다양화 하 고, 고객은 인터넷, IPSec VPN을 사용 하 여 또는 온-프레미스 네트워크를 통해 클라우드 공급자의 직접 연결 솔루션을 사용 하 여 클라우드 네트워크를 상호 연결입니다. 클라우드 네트워크 유용함 시간, 비용, 디자인, 조달, 설치, 테스트 및 작업에 상당한 투자를 요구할 수 있습니다. 

이러한 고객 문제를 해결 하기 위해 Oracle과 Microsoft를 통합된 하는 다중 클라우드 환경을 설정한 합니다. 클라우드 간 네트워킹 연결 하 여 설정 되는 [ExpressRoute](../../../expressroute/expressroute-introduction.md) 회로 사용 하 여 Microsoft Azure에는 [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) OCI 회로. 이 연결 가능성이 여기서 Azure ExpressRoute 피어 링 위치는 인접 한 위치에 또는 OCI FastConnect 동일한 피어 링 위치에 있습니다. 이 설치는 중간 서비스 공급자에 대 한 필요 없이 두 클라우드 간에 안전 하 고 빠른 연결을 허용합니다.

ExpressRoute 및 FastConnect 사용 고객 피어 링 수 있습니다 OCI에서 가상 클라우드 네트워크를 사용 하 여 Azure에서 가상 네트워크는 개인 IP 주소 공간이 겹치지 합니다. 두 네트워크를 피어 링 둘 다 동일한 네트워크에 있는 것 처럼 OCI 가상 클라우드 네트워크의 리소스와 통신 하도록 가상 네트워크의 리소스를 허용 합니다.

## <a name="network-security"></a>네트워크 보안

네트워크 보안 모든 엔터프라이즈 응용 프로그램의 중요 한 구성 요소 이며이 다중 클라우드 솔루션의 중심입니다. ExpressRoute 및 FastConnect를 통해 모든 트래픽을 개인 네트워크를 통해 전달 합니다. 이 구성은 Azure virtual network와 Oracle 가상 클라우드 네트워크 간의 보안 통신에 허용합니다. Azure의 모든 가상 머신에 공용 IP 주소를 제공할 필요가 없습니다. 마찬가지로, OCI의 인터넷 게이트웨이 필요는 없습니다. 모든 통신은 컴퓨터의 개인 IP 주소를 통해 수행 됩니다.

또한 설정할 수 있습니다 [보안 목록](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) OCI 가상 클라우드 네트워크 및 보안 규칙 (Azure에 연결 [네트워크 보안 그룹](../../../virtual-network/security-overview.md)). 가상 네트워크의 컴퓨터 간의 트래픽 흐름을 제어 하려면 이러한 규칙을 사용 합니다. 컴퓨터 수준에서 서브넷 수준에서 뿐만 아니라 가상 네트워크 수준에서 네트워크 보안 규칙을 추가할 수 있습니다.
 
## <a name="identity"></a>ID

Id는 Microsoft와 Oracle 간의 파트너 관계의 핵심 요소 중 하나입니다. 많은 작업을 통합 하 저희 [Oracle Id 클라우드 서비스](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) 사용 하 여 [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). Azure AD는 Microsoft의 클라우드 기반 id 및 액세스 관리 서비스입니다. 사용자가 로그인 하 고 다양 한 리소스에 액세스할 수 있습니다. Azure AD 사용자 및 해당 권한을 관리할 수도 있습니다.

현재이 통합을 사용 하면 Azure Active directory는 하나의 중앙 위치에서 관리할 수 있습니다. Azure AD 디렉터리에 모든 변경 내용을 해당 Oracle 디렉터리와 동기화 하 고 single sign on에 대 한 클라우드 간 Oracle 솔루션 하는 데 사용 됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용 및 OCI에 대 한 백서를 참조 합니다 [Oracle 클라우드](https://docs.cloud.oracle.com/iaas/Content/home.htm) 설명서.
