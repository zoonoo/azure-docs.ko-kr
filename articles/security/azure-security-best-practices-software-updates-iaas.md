---
title: "Microsoft Azure IaaS에서 소프트웨어 업데이트에 대한 모범 사례 | Microsoft Docs"
description: "문서는 Microsoft Azure IaaS 환경에서 소프트웨어 업데이트에 대한 모범사례의 컬렉션을 제공합니다.  조직의 보안 및 규정 준수 담당자를 포함하여, 매일 변경 내용 제어, 소프트웨어 업데이트 및 자산 관리를 처리하는 IT 전문가 및 보안 분석가를 위한 것입니다."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: df6598ee-de5b-48cd-b321-0b2f19d7eeaa
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c52a2d6be31b1a8f581313932c0af87120ccd9fe


---
# <a name="best-practices-for-software-updates-on-microsoft-azure-iaas"></a>Microsoft Azure IaaS에서 소프트웨어 업데이트에 대한 모범 사례
Azure [IaaS](https://azure.microsoft.com/overview/what-is-iaas/) 환경에 대한 모범 사례에 대해 설명하기 전에 소프트웨어 업데이트를 관리하는 시나리오 및 책임의 정의를 이해하는 것이 중요합니다. 아래 다이어그램은 이러한 경계를 이해하는 데 도움이 됩니다.

![클라우드 모델 및 책임](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack-new.png)

맨 왼쪽에 있는 열에는 조직이 고려해야 하는 7가지 책임(다음 섹션에 정의되어 있음)이 나와 있습니다. 이러한 모든 책임은 컴퓨팅 환경의 보안 및 개인 정보와 관련이 있습니다.

데이터 분류 및 책임, 클라이언트 및 끝점 보호는 고객의 영역에서만 발생하는 책임이며, 물리적 책임, 호스트 및 네트워크 책임은 PaaS 및 SaaS 모델의 클라우드 서비스 공급자 영역에서 발생합니다. 

나머지 책임은 고객 및 클라우드 서비스 공급자 간에 공유됩니다. 일부 책임의 경우 CSP 및 고객이 해당 영역의 감사를 비롯한 책임 업무를 함께 관리하고 운영해야 합니다. 예를 들어 Azure Active Directory 서비스를 사용할 때 ID 및 액세스 관리를 고려해 보세요. 다단계 인증과 같은 서비스의 구성은 고객의 책임이지만 효과적인 작동을 보장하는 것은 Microsoft Azure의 책임입니다.

> [!NOTE]
> 클라우드의 공유 책임에 대한 자세한 내용은 [클라우드 컴퓨팅에 대한 공유 책임](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf)을 참조하세요. 
> 
> 

아래 다이어그램에 표시된 것처럼 온-프레미스 리소스와 통신하는 Azure IaaS VM을 사용하는 하이브리드 시나리오에서 이러한 동일한 원칙이 적용됩니다.

![Microsoft Azure를 사용한 일반적인 하이브리드 시나리오](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## <a name="initial-assessment"></a>초기 평가
귀사가 업데이트 관리 시스템을 이미 사용하고 소프트웨어 업데이트 정책을 이미 갖추었더라도 이전의 정책 평가를 자주 다시 확인하고 현재의 요구 사항에 따라 업데이트해야 합니다. 이는 회사에 있는 리소스의 현재 상태를 잘 알고 있다는 것을 의미합니다. 이 상태에 도달하려면 다음을 알아야 합니다.

* 기업에서 실제 및 가상 컴퓨터입니다.
* 이러한 실제 및 가상 컴퓨터에서 각각 실행되는 운영 체제 및 버전입니다.
* 각 컴퓨터에 현재 설치된 소프트웨어가 업데이트됩니다.(서비스 팩 버전, 소프트웨어 업데이트 및 기타 수정)
* 각 컴퓨터가 기업에서 수행하는 함수입니다.
* 각 컴퓨터에서 실행되는 응용 프로그램 및 프로그램입니다.
* 각 컴퓨터에 대한 소유권 및 연락처 정보입니다.
* 자산은 환경 및 해당 상대값에서 보여지며 어떤 영역에 주의 및 보호가 가장 필요한지 결정합니다.
* 새로운 보안 문제 및 보안 수준의 변경 내용을 식별하는 기업에 존재하는 알려진 보안 문제 및 프로세스입니다.  
* 환경을 보호하려면 배포된 대책을 선택합니다.

이 정보를 정기적으로 업데이트해야 하고 소프트웨어 업데이트 관리 프로세스에 포함된 사항을 바로 사용할 수 있어야 합니다.

## <a name="establish-a-baseline"></a>기초 설정
소프트웨어 업데이트 관리 프로세스의 중요한 부분은 기업의 컴퓨터에 대한 운영 체제 버전, 응용 프로그램 및 하드웨어의 초기 표준 설치를 만드는 것입니다. 이를 기초라고 합니다. 기초는 특정 시점에 설정된 상품 및 시스템의 구성입니다. 예를 들어 응용 프로그램 또는 운영 체제 기초는 특정 상태에 컴퓨터 또는 서비스를 다시 빌드하는 기능을 제공합니다.

기초는 기업에 배포해야 하는 소프트웨어 업데이트의 수를 줄이고 규정 준수를 모니터링하는 능력을 증가시켜 잠재적인 문제를 찾고 수정할 수 있는 기본을 제공하고 소프트웨어 업데이트 관리 프로세스를 단순화합니다.

엔터프라이즈의 초기 감사를 수행한 후에 감사에서 가져온 정보를 사용하여 프로덕션 환경 내에서 IT 구성 요소에 대한 작동 기초를 정의해야 합니다. 프로덕션 환경에 배포된 다양한 유형의 하드웨어 및 소프트웨어에 따라 다양한 기초가 필요한 수도 있습니다.

예를 들어 Windows Server 2012를 실행하는 경우 일부 서버는 종료 프로세스를 입력하면 작동 중지를 방지 하기 위한 소프트웨어 업데이트가 필요합니다. 이러한 서버에 대한 기초는 해당 소프트웨어 업데이트를 포함해야 합니다.

대규모 조직에서 동일한 버전의 소프트웨어 및 소프트웨어 업데이트를 사용하여 엔터프라이즈의 컴퓨터를 자산 범주로 나누고 표준 기초에서 각 범주를 유지하는 데 유용합니다. 그런 다음 이러한 자산 범주를 소프트웨어 업데이트 배포의 우선 순위를 지정하는 데 사용할 수 있습니다.

## <a name="subscribe-to-the-appropriate-software-update-notification-services"></a>적절한 소프트웨어 업데이트 알림 서비스 구독
엔터프라이즈에서 사용 중인 소프트웨어의 초기 감사를 수행한 후에 각 소프트웨어 제품 및 버전에 새 소프트웨어 업데이트의 알림을 수신하기 위한 최상의 방법을 결정해야 합니다. 소프트웨어 제품에 따라 최상의 알림 방법은 전자 메일 알림, 웹 사이트, 또는 컴퓨터 공지일 수 있습니다.

예를 들어 Microsoft 보안 대응 센터(MSRC)는 Microsoft 제품에 대한 모든 보안 관련 문제에 응답하고 Microsoft 보안 게시판 서비스, 즉, 새로 식별된 취약점 및 취약점을 해결하기 위해 릴리스된 소프트웨어 업데이트에 대한 무료 전자 메일 알림을 제공합니다. http://www.microsoft.com/technet/security/bulletin/notify.mspx에서 이 서비스를 구독할 수 있습니다.

## <a name="software-update-considerations"></a>소프트웨어 업데이트 고려 사항
엔터프라이즈에서 사용 중인 소프트웨어의 초기 감사를 수행한 후에 요구 사항 결정하여 소프트웨어 업데이트 관리 시스템을 설치해야 하며 이는 사용 중인 소프트웨어 업데이트 관리 시스템에 종속됩니다. WSUS는 [Windows Server Update Service를 사용한 모범 사례](https://technet.microsoft.com/library/Cc708536)를 읽고 System Center는 [구성 관리자에서 소프트웨어 업데이트에 대한 계획](https://technet.microsoft.com/library/gg712696)을 읽습니다.

그러나 다음에 나오는 섹션처럼 사용하는 솔루션에 상관없이 적용할 수 있는 일부 일반적인 고려 사항 및 모범 사례가 있습니다.

### <a name="setting-up-the-environment"></a>환경 설정
소프트웨어 업데이트 관리 환경을 설치할 계획이라면 다음 사례를 고려합니다.

* **안정된 기준에 따른 프로덕션 소프트웨어 업데이트 컬렉션 만들기**: 일반적으로 안정된 기준을 사용하여 소프트웨어 업데이트 인벤토리 및 배포에 대한 컬렉션을 만들면 소프트웨어 업데이트 관리 프로세스의 모든 단계를 간소화할 수 있습니다. 안정적인 조건이 설치된 클라이언트 운영 체제 버전 및 서비스 팩 수준, 시스템 역할 또는 대상 조직을 포함할 수 있습니다.
* **참조 컴퓨터를 포함하는 사전 프로덕션 컬렉션 만들기**: 사전 프로덕션 컬렉션은 기업에서 실행되는 운영 체제 버전, 비즈니스 소프트웨어의 줄 및 다른 소프트웨어의 대표적인 구성을 포함해야 합니다.

또한 소프트웨어 업데이트 서버가 클라우드의 Azure IaaS 인프라 또는 온-프레미스에 위치한 경우 이를 고려해 야 합니다. 온-프레미스 리소스와 Azure 인프라 간의 트래픽 양을 평가해야 하기 때문에 중요한 결정입니다. 온-프레미스 인프라를 Azure에 연결하는 방법에 대한 자세한 내용은 [Microsoft Azure Virtual Network에 온-프레미스 네트워크 연결](https://technet.microsoft.com/library/Dn786406.aspx)을 읽습니다.

또한 업데이트 서버가 위치할 곳을 결정하는 설계 옵션은 현재 인프라 및 현재 사용 중인 소프트웨어 업데이트 시스템에 따라 다릅니다. WSUS는 [조직에서 Windows Server Update Service 배포](https://technet.microsoft.com/library/hh852340.aspx)를 읽고 System Center Configuration Manager는 [구성 관리자에서 사이트 및 계층에 대한 계획](https://technet.microsoft.com/library/Gg712681.aspx)을 읽습니다.

### <a name="backup"></a>백업
정기적인 백업은 소프트웨어 업데이트 관리 플랫폼 자체 뿐만 아니라 업데이트되는 서버에도 중요합니다. [변경 관리 프로세스](https://technet.microsoft.com/library/cc543216.aspx)를 갖춘 조직은 서버를 업데이트해야 하는 이유, 예상된 가동 중지 시간 및 가능한 영향에 대한 이유를 정당화하기 위해 IT를 필요로 합니다. 업데이트가 실패하는 경우 롤백 구성이 준비되었는지를 확인하려면 시스템을 정기적으로 백업해야 합니다.

Azure IaaS에 대한 일부 백업 옵션은 다음과 같습니다.

* [Data Protection Manager를 사용하여 Azure IaaS 워크로드 보호](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/)
* [Azure 가상 컴퓨터 백업](../backup/backup-azure-vms.md)

### <a name="monitoring"></a>모니터링
정기적으로 보고서를 실행하여 권한이 부여된 각 소프트웨어 업데이트에 대해 누락 또는 설치된 업데이트나 불완전한 상태인 업데이트의 수를 모니터링합니다. 마찬가지로 아직 권한이 없는 소프트웨어 업데이트에 대한 보고는 배포 결정을 보다 용이하게 할 수 있습니다.

또한 다음 작업을 고려해야 합니다.

* 회사의 모든 컴퓨터에 적용 가능하고 설치된 보안 업데이트의 감사를 수행합니다.
* 권한을 부여하고 해당 컴퓨터에 업데이트를 배포합니다.
* 인벤토리를 추적하고 회사의 모든 컴퓨터에 설치 상태 및 진행률을 업데이트합니다.

또한 이 기사에서 설명한 일반적인 고려 사항 외에도 각 제품의 모범 사례를 고려해야 합니다. 예를 들어 SQL Server을 사용하는 Azure에 VM이 있는 경우 해당 제품에 대한 소프트웨어 업데이트 권장 사항을 수행해야 합니다.

## <a name="next-steps"></a>다음 단계
Azure IaaS 내에서 이 문서에 설명된 지침을 사용하여 가상 컴퓨터에 대한 소프트웨어 업데이트에 가장 적합한 옵션을 결정하도록 지원합니다. 기존 데이터 센터 및 Azure IaaS에서  소프트웨어 업데이트 모범 사례 간의 많은 공통점이 있습니다. 따라서 현재 소프트웨어 업데이트 정책을 평가하여 Azure VM을 포함하고 전체 소프트웨어 업데이트 프로세스의 이 문서에서 관련된 모범 사례를 포함하는 것이 좋습니다 




<!--HONumber=Nov16_HO3-->


