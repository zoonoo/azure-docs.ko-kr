---
title: 마이크로소프트 Azure를 오라클 클라우드 인프라와 통합 | 마이크로 소프트 문서
description: Microsoft Azure에서 실행되는 Oracle 앱을 OCI(오라클 클라우드 인프라)의 데이터베이스와 통합하는 솔루션에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: e70eedcfcdf548965b79e4a48a3a8bfa643f0396
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687437"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>마이크로소프트 Azure와 오라클 클라우드 인프라를 통합한 오라클 애플리케이션 솔루션

Microsoft와 오라클은 낮은 대기 시간, 높은 처리량의 교차 클라우드 연결을 제공하기 위해 파트너십을 맺어 두 클라우드 모두의 장점을 활용할 수 있도록 했습니다. 

이 클라우드 간 연결을 사용하여 다중 계층 응용 프로그램을 분할하여 OCI(Oracle 클라우드 인프라)에서 데이터베이스 계층을 실행하고 Microsoft Azure의 응용 프로그램 및 기타 계층을 실행할 수 있습니다. 이 환경은 단일 클라우드에서 전체 솔루션 스택을 실행하는 것과 유사합니다. 

Azure 인프라에 전적으로 Oracle 솔루션을 배포하려는 경우 [Oracle VM 이미지 및 Microsoft Azure에서의 배포를](oracle-vm-solutions.md)참조하십시오.

## <a name="scenario-overview"></a>시나리오 개요

크로스 클라우드 연결은 OCI에서 호스팅되는 데이터베이스 서비스의 이점을 누리면서 Azure 가상 머신에서 Oracle의 업계 최고의 응용 프로그램 및 사용자 지정 응용 프로그램을 실행할 수 있는 솔루션을 제공합니다. 

> [!IMPORTANT]
> 오라클은 2020년 5월까지 Azure/Oracle Cloud 상호 연결 솔루션을 사용할 때 이러한 응용 프로그램을 Azure에서 실행하도록 인증합니다.

클라우드 간 구성에서 실행할 수 있는 응용 프로그램은 다음과 같습니다.

* E-비즈니스 스위트
* JD Edwards EnterpriseOne
* PeopleSoft
* 오라클 리테일 애플리케이션
* 오라클 하이페리온 재무 관리

다음 다이어그램은 연결된 솔루션에 대한 상위 개요입니다. 간단히 하기 위해 다이어그램에는 응용 프로그램 계층과 데이터 계층만 표시됩니다. 응용 프로그램 아키텍처에 따라 솔루션에 Azure의 웹 계층과 같은 추가 계층이 포함될 수 있습니다. 자세한 내용은 다음 섹션을 참조하세요.

![Azure OCI 솔루션 개요](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>지역 가용성 

클라우드 간 연결은 다음 리전으로 제한됩니다.
* 미국 동부(동부) & OCI 애쉬번(미국 동부)
* Azure UK 사우스(uksouth) & OCI 런던(영국 남부)
* Azure 캐나다 중부(캐나다 중부) & OCI 토론토(캐나다 남동부)
* Azure 서유럽(서유럽) & OCI 암스테르담(네덜란드 북서부)
* Azure 일본 동부(일본) & OCI 도쿄(일본 동부)

## <a name="networking"></a>네트워킹

엔터프라이즈 고객은 다양한 비즈니스 및 운영상의 이유로 여러 클라우드를 통해 워크로드를 다양화하고 배포하는 경우가 많습니다. 다각화하기 위해 고객은 인터넷, IPSec VPN을 사용하거나 온-프레미스 네트워크를 통해 클라우드 공급자의 직접 연결 솔루션을 사용하여 클라우드 네트워크를 상호 연결합니다. 클라우드 네트워크를 상호 연결하려면 시간, 비용, 설계, 조달, 설치, 테스트 및 운영에 상당한 투자가 필요할 수 있습니다. 

이러한 고객 과제를 해결하기 위해 오라클과 Microsoft는 통합 멀티 클라우드 환경을 지원했습니다. 크로스 클라우드 네트워킹은 마이크로 소프트 Azure의 [ExpressRoute](../../../expressroute/expressroute-introduction.md) 회로를 OCI의 [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) 회로와 연결하여 설정됩니다. 이 연결은 Azure ExpressRoute 피어링 위치가 OCI FastConnect와 같은 피어링 위치에 근접하거나 있는 경우에 가능합니다. 이 설정을 사용하면 중간 서비스 공급자없이 두 클라우드 간에 안전하고 빠른 연결을 할 수 있습니다.

고객은 ExpressRoute 및 FastConnect를 사용하여 개인 IP 주소 공간이 겹치지 않는 경우 OCI의 가상 클라우드 네트워크를 사용하여 Azure의 가상 네트워크를 피어로 연결할 수 있습니다. 두 네트워크를 피어링하면 가상 네트워크의 리소스가 동일한 네트워크에 있는 것처럼 OCI 가상 클라우드 네트워크의 리소스와 통신할 수 있습니다.

## <a name="network-security"></a>네트워크 보안

네트워크 보안은 모든 엔터프라이즈 애플리케이션의 중요한 구성 요소이며 이 멀티 클라우드 솔루션의 핵심입니다. 익스프레스루트및패스트커넥트에 가는 모든 트래픽은 개인 네트워크를 통해 전달됩니다. 이 구성을 사용하면 Azure 가상 네트워크와 Oracle 가상 클라우드 네트워크 간의 보안 통신이 가능합니다. Azure의 모든 가상 컴퓨터에 공용 IP 주소를 제공할 필요가 없습니다. 마찬가지로 OCI에는 인터넷 게이트웨이가 필요하지 않습니다. 모든 통신은 컴퓨터의 개인 IP 주소를 통해 발생합니다.

또한 OCI 가상 클라우드 네트워크 및 보안 규칙(Azure [네트워크 보안 그룹에](../../../virtual-network/security-overview.md)연결)에 보안 [목록을](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) 설정할 수 있습니다. 이러한 규칙을 사용하여 가상 네트워크의 컴퓨터 간에 흐르는 트래픽을 제어합니다. 네트워크 보안 규칙은 컴퓨터 수준, 서브넷 수준 및 가상 네트워크 수준에서 추가할 수 있습니다.
 
## <a name="identity"></a>ID

ID는 Microsoft와 오라클 간의 파트너십의 핵심 요소 중 하나입니다. [오라클 ID 클라우드](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) 서비스(IDCS)를 [Azure Active Directory(Azure](../../../active-directory/index.yml) AD)와 통합하기 위한 중요한 작업이 수행되었습니다. Azure AD는 Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스입니다. 사용자가 로그인하고 다양한 리소스에 액세스하는 데 도움이 됩니다. Azure AD를 사용하면 사용자 및 해당 권한을 관리할 수도 있습니다.

현재 이 통합을 사용하면 Azure Active Directory인 하나의 중앙 위치에서 관리할 수 있습니다. Azure AD는 디렉터리에서 변경 내용을 해당 Oracle 디렉터리와 동기화하며 단일 사인온에서 클라우드 Oracle 솔루션에 사용됩니다.

## <a name="next-steps"></a>다음 단계

Azure와 OCI 간의 [교차 클라우드 네트워크를](configure-azure-oci-networking.md) 시작하세요. 

OCI에 대한 자세한 정보 및 백서는 [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) 설명서를 참조하십시오.
