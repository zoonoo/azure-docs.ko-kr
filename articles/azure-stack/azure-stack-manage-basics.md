---
title: Azure Stack 관리 기본 사항 | Microsoft Docs
description: Azure Stack을 관리 하기 위해 알아야 할 내용을 알아보세요.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: ba1e7d7f9226b3ca42d4117be33b9c236f0557b0
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867323"
---
# <a name="azure-stack-administration-basics"></a>Azure Stack 관리 기본 사항
Azure Stack 관리 접하는 경우 알아야 할 몇 가지 있습니다. 이 문서에서는 Azure Stack 운영자 역할의 개요 및 생산성을 높이려는 신속 하 게 사용자에 게 필요한를 제공 합니다.

## <a name="understand-the-builds"></a>빌드를 이해 합니다.

### <a name="integrated-systems"></a>통합 시스템

Azure Stack 통합 시스템을 사용 하는 경우 Azure Stack의 업데이트 된 버전은 업데이트 패키지를 통해 배포 됩니다. 이러한 패키지를 가져올 수 있으며 관리자 포털에서 업데이트 타일을 사용 하 여 적용할 수 있습니다. ASDK 설치를 업데이트 하는 지원 되지 않습니다. 
 
### <a name="development-kit"></a>개발 키트

Azure Stack 개발 키트를 사용 하는 경우 검토 합니다 [Azure Stack 이란?](./asdk/asdk-what-is.md) 문서 개발 키트 및 제한 사항의 용도 이해 해야 합니다. 개발 키트를 사용 하 여 샌드박스로"," Azure Stack을 평가 하 고 개발 하 고 수 있는 비-프로덕션 환경에서 앱을 테스트 해야 합니다. (배포 정보에 대 한 참조를 [Azure Stack 개발 키트 배포](./asdk/asdk-install.md) 문서입니다.)

Azure와 같은에서는 빠른 혁신. 정기적으로 새 빌드 출시할 예정입니다. 개발 키트를 실행 하는 경우 이동할 최신 빌드를 수행 해야 합니다 [Azure Stack 다시 배포](./asdk/asdk-redeploy.md)합니다. 업데이트 패키지를 적용할 수 없습니다. 이 프로세스 시간은 걸리지만 장점은 최신 기능을 시도해 볼 수 있습니다. 웹 사이트에 대 한 개발 키트 설명서는 최신 릴리스 빌드를 반영합니다.

## <a name="learn-about-available-services"></a>사용 가능한 서비스에 대해 알아보기

서비스의 수 사용할 수 있도록 사용자에 게 인식을 해야 합니다. Azure Stack Azure 서비스의 하위 집합을 지원 합니다. 지원 되는 서비스 목록을 개선 될 것입니다.

**기본 서비스**

기본적으로 Azure Stack에는 다음 "기본 서비스"이 포함 되어 Azure Stack을 배포 하는 경우:

- 컴퓨팅
- Storage
- 네트워킹
- Key Vault

이러한 기본 서비스를 사용 하 여 최소 구성으로 사용자에 게 인프라-as a Service (IaaS)를 제공할 수 있습니다.

**추가 서비스**

현재 다음과 같은 추가 플랫폼-as a Service (PaaS) 서비스를 지원합니다.

- App Service
- Azure 기능
- SQL 및 MySQL 데이터베이스

이러한 서비스 수 적용 하기 전에 사용할 수 있는 사용자에 게 추가 구성이 필요 합니다. 자세한 내용은 "자습서" 및 Azure Stack 연산자 설명서의 "방법 guides\Offer 서비스" 섹션을 참조 하세요.

**서비스 로드맵**

Azure Stack은 Azure 서비스에 대 한 지원을 추가할 계속 됩니다. 예상된 로드맵 상에 대 한 참조를 [Azure Stack: Azure의 확장](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) 백서입니다. 모니터링할 수도 있습니다는 [Azure Stack 블로그 게시물](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) 새 공지 사항에 대 한 합니다.

## <a name="what-account-should-i-use"></a>사용할 계정은?
Azure Stack을 관리할 때 고려해 야 하는 몇 가지 계정 고려 사항이 있습니다. 배포에서 특히 Windows Server Active Directory Federation Services (AD FS)를 id 공급자로 Azure Active Directory (Azure AD) 대신 사용합니다. 다음 계정 고려 사항 ASDK 배포와 Azure Stack 통합 시스템에 적용 됩니다.


|계좌|Azure AD|AD FS|
|-----|-----|-----|
|로컬 관리자 (. \Administrator)|ASDK 호스트 관리자|ASDK 호스트 관리자|
|AzureStack\AzureStackAdmin|ASDK 호스트 관리자<br><br>Azure Stack 관리 포털에 로그인 할 수 있습니다.<br><br>보기 및 Service Fabric 링 관리에 대 한 액세스|ASDK 호스트 관리자<br><br>Azure Stack 관리 포털에 액세스할 수 없음<br><br>보기 및 Service Fabric 링 관리에 대 한 액세스<br><br>더 이상 기본 공급자 구독 (DPS)의 소유자|
|AzureStack\CloudAdmin|액세스 하 고 권한 있는 끝점 내에서 허용 된 명령을 실행할 수 있습니다.|액세스 하 고 권한 있는 끝점 내에서 허용 된 명령을 실행할 수 있습니다.<br><br>ASDK 호스트에 로그인 할 수 없습니다.<br><br>기본 공급자 구독 (DPS)의 소유자|
|Azure AD 전역 관리자|설치 중에 사용<br><br>기본 공급자 구독 (DPS)의 소유자|해당 없음|
|

## <a name="what-tools-do-i-use-to-manage"></a>관리 하려면 어떤 도구를 사용 합니까?
 
사용할 수는 [관리자 포털](azure-stack-manage-portals.md) 또는 PowerShell을 Azure Stack을 관리 합니다. 기본 개념을 알아보고 하는 가장 쉬운 방법은 포털을 통해 됩니다. PowerShell을 사용 하려는 경우에 준비 단계가 있습니다. 수행 해야 합니다 [설치](azure-stack-powershell-install.md) PowerShell [다운로드](azure-stack-powershell-download.md) 추가 모듈 및 [구성](azure-stack-powershell-configure-admin.md) PowerShell.

Azure Stack의 기본 배포, 관리 및 조직 메커니즘으로 Azure Resource Manager를 사용합니다. Azure Stack을 관리 하 여 사용자를 지원할 수 있도록 하려는 경우 Resource Manager에 대 한 배워야 합니다. 참조 된 [Azure Resource Manager를 사용 하 여 시작](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) 백서입니다.

## <a name="your-typical-responsibilities"></a>일반적인 사용자의 책임

사용자가 서비스를 사용 하려고 합니다. 자신의 관점에서 이러한 서비스를 사용할 수 있도록 주 역할을 맡고 합니다. 서비스를 제공 하 고 계획, 제품 및 할당량을 만들어 해당 서비스를 사용할 수 있도록 해야 합니다. 자세한 내용은 [Azure Stack에서 서비스를 제공 하는 개요](azure-stack-offer-services-overview.md)합니다. 

항목을 추가 하 고 해야 [marketplace](azure-stack-marketplace.md), 가상 머신 이미지 등. 가장 쉬운 방법은 [Azure에서 Azure Stack marketplace 항목 다운로드](azure-stack-download-azure-marketplace-item.md)합니다.

> [!NOTE]
> 에 계획, 제품 및 서비스를 테스트 하려는 경우 사용 해야 합니다 [사용자 포털](azure-stack-manage-portals.md); 관리자 포털.

서비스를 제공 하는 것 외에도 모든 일반 임무 연산자의 Azure Stack을 실행 되도록 해야 합니다. 이러한 의무는 다음과 같습니다.

- 사용자 계정 추가 (에 대 한 [Azure Active Directory](azure-stack-add-new-user-aad.md) 배포를 기다리거나 [Active Directory Federation Services](azure-stack-add-users-adfs.md) 배포)
- [역할 기반 액세스 제어 (RBAC) 역할을 할당](azure-stack-manage-permissions.md) (이 제한 되지 않습니다 관리자에 게.)
- [인프라 상태 모니터링](azure-stack-monitor-health.md)
- 관리할 [네트워크](azure-stack-viewing-public-ip-address-consumption.md) 하 고 [저장소](azure-stack-manage-storage-accounts.md) 리소스
- 예를 들어 잘못 된 하드웨어를 교체 [실패 한 디스크를 교체](azure-stack-replace-disk.md)합니다.

## <a name="what-to-tell-your-users"></a>사용자에 게 알릴 내용

Azure Stack에서 서비스를 사용 하는 방법, 환경에 연결 하는 방법 및 제품을 구독 하는 방법을 알고 있는 사용자에 게 해야 합니다. 사용자에 게 제공 하는 것이 좋습니다는 모든 사용자 지정 문서 외에도 Azure Stack 사용자 설명서 사이트에 사용자를 보낼 수 있습니다.

**Azure Stack에서 서비스를 사용 하는 방법 이해**

있으면 정보가 사용자에 게 서비스를 사용 하 여 Azure Stack에서 앱을 작성 하기 전에 이해 해야 합니다. 예를 들어, 특정 PowerShell 및 API 버전 요구 사항이 있습니다. 또한 Azure의 서비스와 Azure Stack에서 해당 서비스 간의 몇 가지 기능 델타 있습니다. 사용자가 다음 문서를 검토 하 고 있는지 확인 합니다.

- [주요 고려 사항: 서비스를 사용 하 여 또는 Azure Stack에 대 한 앱 빌드](user/azure-stack-considerations.md)
- [Azure Stack에서 Virtual Machines에 대 한 고려 사항](user/azure-stack-vm-considerations.md)
- [저장소: 차이점 및 고려 사항](user/azure-stack-acs-differences.md)

이 문서의 정보를 Azure의 서비스 및 Azure Stack 간의 차이점을 요약합니다. 전역 Azure 설명서에서 Azure 서비스에 사용할 수 있는 정보를 보완 합니다.

**사용자로 Azure Stack에 연결**

개발 키트 환경에서 사용자 개발 키트 호스트에 대 한 원격 데스크톱 액세스 없는 경우는 연결을 구성 해야 가상 사설망 (VPN) Azure Stack에 액세스 하기 전에. 참조 [Azure Stack에 연결](azure-stack-connect-azure-stack.md)합니다. 

사용자가 알아야 할 하는 방법 [사용자 포털에 액세스할](user/azure-stack-use-portal.md) 또는 PowerShell을 통해 연결 하는 방법입니다. 통합된 시스템 환경에서 사용자 포털 주소 배포 마다 다릅니다. 올바른 URL을 사용 하 여 사용자에 게 제공 해야 합니다.

PowerShell을 사용 하는 경우 서비스 사용 하기 전에 리소스 공급자를 등록 하려면 사용자가 할 수 있습니다. (리소스 공급자는 서비스를 관리 하는 데 사용 합니다. 네트워킹 리소스 공급자 가상 네트워크, 네트워크 인터페이스 및 부하 분산 장치 등의 리소스를 관리 하는 예를 들어.) 시켜야 하 [설치](user/azure-stack-powershell-install.md) PowerShell [다운로드](user/azure-stack-powershell-download.md) 추가 모듈 및 [구성](user/azure-stack-powershell-configure-user.md) PowerShell (리소스 공급자 등록을 포함).

**제품 구독**

시켜야 하는 사용자가 서비스에 액세스 하기 전에 [제품 구독](azure-stack-subscribe-plan-provision-vm.md) 연산자로 만든 합니다.

## <a name="where-to-get-support"></a>지원을 받을 수 있는 위치

### <a name="integrated-systems"></a>통합 시스템

통합된 시스템에 대 한는 통합된 에스컬레이션 및 Microsoft와 원래 장비 제조업체 (OEM) 하드웨어 파트너 간의 해결 프로세스.

클라우드 서비스 문제 이면 지원은 통해 Microsoft 고객 지원 서비스 (CSS) 제공 됩니다. 관리자 포털의 오른쪽 위 모서리에 있는 도움말 및 지원 (물음표) 아이콘을 클릭 하 고 클릭 **새 지원 요청**, 지원 요청을 직접 시작할 수 있는 사이트 열립니다.

배포에 문제가 있으면 패치 및 업데이트, 하드웨어 (필드 교체 장치 포함) 및 하드웨어 수명 주기 호스트에서 실행 되는 소프트웨어와 같은 하드웨어 브랜드 소프트웨어를 먼저에 문의 OEM 하드웨어 공급 업체에 문의 합니다.

다른 작업에 대 한 Microsoft CSS에 문의 합니다.

### <a name="development-kit"></a>개발 키트

개발 키트에서 지원 관련 질문을 올릴 수는 [Microsoft 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)합니다. 관리자 포털의 오른쪽 위 모서리에 있는 도움말 및 지원 (물음표) 아이콘을 클릭 하 고 클릭 **새 지원 요청**, 직접 포럼 사이트 열립니다. 이러한 포럼을 정기적으로 모니터링 합니다. 개발 키트는 평가 환경, 이므로 Microsoft CSS를 통해 제공 되는 공식 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

[Azure Stack의 지역 관리](azure-stack-region-management.md)


