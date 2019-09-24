---
title: Oracle 클라우드 인프라와 Microsoft Azure 통합 | Microsoft Docs
description: Microsoft Azure에서 실행 되는 Oracle 앱을 OCI (Oracle Cloud Infrastructure)의 데이터베이스와 통합 하는 솔루션에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 9947d28cbde7f4804283e03cc07093b9240ca6bf
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240997"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Oracle 응용 프로그램 솔루션 Microsoft Azure 및 Oracle 클라우드 인프라 통합 (미리 보기)

Microsoft와 Oracle은 낮은 대기 시간, 높은 처리량의 클라우드 간 연결을 제공 하 여 두 클라우드의 가장 뛰어난 이점을 활용할 수 있도록 합니다. 

이 클라우드 간 연결을 사용 하 여 다중 계층 응용 프로그램을 분할 하 여 Oracle 클라우드 인프라 (OCI)에서 데이터베이스 계층을 실행 하 고 응용 프로그램 및 다른 계층을 Microsoft Azure 수 있습니다. 이 환경은 단일 클라우드에서 전체 솔루션 스택을 실행 하는 것과 비슷합니다. 

> [!IMPORTANT]
> 이 클라우드 간 기능은 현재 미리 보기로 제공 되며 [제한이 적용](#preview-limitations)됩니다. Azure와 OCI 간에 짧은 대기 시간 연결을 사용 하도록 설정 하려면 먼저 Azure 구독이이 기능에 대해 목록으로 표시 되어야 합니다. 이 간단한 [설문 조사 양식을](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu)완료 하 여 미리 보기에 등록 해야 합니다. 구독이 등록되면 이메일 회신을 받게 됩니다. 확인 이메일을 받을 때까지는 기능을 사용할 수 없습니다. 이 미리 보기를 사용 하도록 설정 하려면 Microsoft 담당자에 게 문의할 수도 있습니다. 미리 보기 기능에 대 한 액세스는 가용성 및 Microsoft에 의해 제한 될 수 있습니다. 설문 조사 완료는 액세스를 보장 하지 않습니다. 이 미리 보기는 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에 사용할 수 없습니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 Microsoft Azure 미리 보기에 대 한 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 을 참조 하세요. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

Oracle 솔루션을 완전히 Azure 인프라에 배포 하는 데 관심이 있는 경우 [ORACLE VM 이미지 및 Microsoft Azure에 대 한 배포](oracle-vm-solutions.md)를 참조 하세요.

## <a name="scenario-overview"></a>시나리오 개요

클라우드 간 연결은 OCI에서 호스팅된 데이터베이스 서비스의 이점을 누릴 수 있는 동시에 Azure virtual machines에서 Oracle의 업계 최고의 응용 프로그램 및 사용자 지정 응용 프로그램을 실행할 수 있는 솔루션을 제공 합니다. 

클라우드 간 구성에서 실행할 수 있는 응용 프로그램은 다음과 같습니다.

* E-비즈니스 제품군
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle 소매 응용 프로그램
* Oracle Hyperion 금융 관리

다음 다이어그램은 연결 된 솔루션에 대 한 개략적인 개요입니다. 간단한 설명을 위해 다이어그램에는 응용 프로그램 계층과 데이터 계층만 표시 됩니다. 응용 프로그램 아키텍처에 따라 솔루션에는 Azure의 웹 계층과 같은 추가 계층이 포함 될 수 있습니다. 자세한 내용은 다음 섹션을 참조하세요.

![Azure OCI 솔루션 개요](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>미리 보기 제한 사항

* 미리 보기로 제공 되는 클라우드 간 연결은 Azure 미국 동부 (영국 남부 & estorage.westcentralus) 지역 및 OCI Ashburn (미국 동부) & 런던 (영국 남부) 지역으로 제한 됩니다. 영국 남부의 경우 대기 시간에 대 한 연결 간 연결을 배포할 때 OCI에서 가용성 도메인 1 (AD 1)을 사용 하세요.

## <a name="networking"></a>네트워킹

기업 고객은 다양 한 비즈니스 및 운영 상의 이유로 여러 클라우드에서 워크 로드를 다양화 및 배포 하도록 선택 하는 경우가 많습니다. 다양화 고객은 인터넷, IPSec VPN을 사용 하거나 온-프레미스 네트워크를 통해 클라우드 공급자의 직접 연결 솔루션을 사용 하 여 클라우드 네트워크를 상호 연결 합니다. Interconnecting 클라우드 네트워크는 시간, 비용, 설계, 조달, 설치, 테스트 및 운영에 상당한 투자를 요구할 수 있습니다. 

이러한 고객 문제를 해결 하기 위해 Oracle과 Microsoft는 통합 된 다중 클라우드 환경을 사용 하도록 설정 했습니다. 클라우드 간 네트워킹은 Microsoft Azure에서 [express](../../../expressroute/expressroute-introduction.md) 경로 회로를 OCI의 [fastconnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) 회로와 연결 하 여 설정 합니다. 이러한 연결은 Azure Express 경로 피어 링 위치가 OCI FastConnect와 동일한 피어 링 위치에 있거나 일치 하는 경우에 발생할 수 있습니다. 이 설정을 사용 하면 중간 서비스 공급자 없이도 두 클라우드 간에 안전 하 고 빠른 연결을 사용할 수 있습니다.

Express 경로 및 FastConnect를 사용 하는 고객은 개인 IP 주소 공간이 겹치지 않는 경우 OCI의 가상 클라우드 네트워크를 사용 하 여 Azure의 가상 네트워크를 피어 링 할 수 있습니다. 두 네트워크를 피어 링 하면 가상 네트워크의 리소스가 동일한 네트워크에 있는 것 처럼 OCI 가상 클라우드 네트워크의 리소스와 통신할 수 있습니다.

## <a name="network-security"></a>네트워크 보안

네트워크 보안은 모든 엔터프라이즈 응용 프로그램의 중요 한 구성 요소 이며,이 다중 클라우드 솔루션의 핵심입니다. Express 경로 및 FastConnect를 통해 전송 되는 트래픽은 개인 네트워크를 통해 전달 됩니다. 이 구성은 Azure 가상 네트워크와 Oracle 가상 클라우드 네트워크 간의 보안 통신을 허용 합니다. Azure의 가상 머신에 공용 IP 주소를 제공할 필요는 없습니다. 마찬가지로, OCI에는 인터넷 게이트웨이가 필요 하지 않습니다. 모든 통신은 컴퓨터의 개인 IP 주소를 통해 수행 됩니다.

또한 OCI 가상 클라우드 네트워크 및 보안 규칙 (Azure [네트워크 보안 그룹](../../../virtual-network/security-overview.md)에 연결 됨)에 대 한 [보안 목록을](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) 설정할 수 있습니다. 이러한 규칙을 사용 하 여 가상 네트워크의 컴퓨터 간 트래픽 흐름을 제어할 수 있습니다. 네트워크 보안 규칙은 가상 네트워크 수준 뿐만 아니라 컴퓨터 수준에서 서브넷 수준으로 추가할 수 있습니다.
 
## <a name="identity"></a>ID

Id는 Microsoft와 Oracle 간의 파트너 관계의 핵심 핵심 요소 중 하나입니다. [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (idcs)를 [AZURE ACTIVE DIRECTORY](../../../active-directory/index.yml) (Azure AD)와 통합 하기 위해 많은 작업이 수행 되었습니다. Azure AD는 Microsoft의 클라우드 기반 id 및 액세스 관리 서비스입니다. 사용자가 로그인 하 고 다양 한 리소스에 액세스 하는 데 도움이 됩니다. Azure AD를 사용 하 여 사용자와 해당 사용 권한을 관리할 수도 있습니다.

현재이 통합을 사용 하면 Azure Active Directory 한 중앙 위치에서 관리할 수 있습니다. Azure AD는 디렉터리의 모든 변경 내용을 해당 하는 Oracle 디렉터리와 동기화 하며, 클라우드 간 Oracle 솔루션을 Single Sign-On 하는 데 사용 됩니다.

## <a name="next-steps"></a>다음 단계

Azure와 OCI 간의 [클라우드 간 네트워크](configure-azure-oci-networking.md) 를 시작 합니다. 

OCI에 대 한 자세한 내용 및 백서는 [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) 설명서를 참조 하세요.
