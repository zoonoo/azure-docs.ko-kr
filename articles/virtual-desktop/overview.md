---
title: Windows Virtual Desktop이란? - Azure
description: Windows Virtual Desktop의 개요입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e62b3c551f41bca0055f35cf6bf62c59d921c73b
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80294825"
---
# <a name="what-is-windows-virtual-desktop"></a>Windows Virtual Desktop이란? 

Windows Virtual Desktop은 클라우드에서 실행되는 데스크톱 및 앱 가상화 서비스입니다.

Azure에서 Windows Virtual Desktop을 실행하면 다음과 같은 작업이 가능합니다.

* 확장 가능한 완전한 Windows 10을 제공하는 다중 세션 Windows 10 배포 설정
* Office 365 ProPlus를 가상화하고 다중 사용자 가상 시나리오에서 실행되도록 최적화
* Windows 7 가상 데스크톱에 무료 기간 연장 보안 업데이트 제공
* 기존 RDS(원격 데스크톱 서비스)와 Windows Server 데스크톱 및 앱을 컴퓨터로 가져오기
* 데스크톱 및 앱 가상화
* 통합 관리 환경을 사용하여 Windows 10/Windows Server/Windows 7 데스크톱 및 앱 관리

## <a name="introductory-video"></a>소개 비디오

Windows Virtual Desktop의 고유한 이유와 이 비디오의 새로운 기능에 대해 알아봅니다.

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Windows Virtual Desktop에 대한 자세한 비디오는 [재생 목록](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev)을 참조하세요.

## <a name="key-capabilities"></a>주요 기능

Windows Virtual Desktop을 사용하면 다음과 같이 확장 가능하고 유연한 환경을 설정할 수 있습니다.

* 추가 게이트웨이 서버를 실행할 필요 없이 Azure 구독에 완전한 데스크톱 가상화 환경을 만듭니다.
* 다양한 워크로드를 수용하는 데 필요한 만큼 호스트 풀을 게시합니다.
* Azure 갤러리에서 프로덕션 워크로드 또는 테스트에 사용할 사용자 고유의 이미지를 가져옵니다.
* 풀링된 다중 세션 리소스를 사용하여 비용을 줄입니다. Windows Server의 Windows Virtual Desktop 및 RDSH(원격 데스크톱 세션 호스트) 역할에만 제공되는 새 Windows 10 Enterprise 다중 세션 기능을 사용하면 가상 머신의 수와 OS(운영 체제) 오버헤드를 대폭 줄이면서도 사용자에게 동일한 리소스를 제공할 수 있습니다.
* 개인용(영구) 데스크톱을 통해 개별 소유권을 제공합니다.

다음과 같이 가상 데스크톱을 배포 및 관리할 수 있습니다.

* Windows Virtual Desktop PowerShell 및 REST 인터페이스를 사용하여 호스트 풀을 구성하고, 앱 그룹을 만들고, 사용자를 할당하고, 리소스를 게시합니다.
* 단일 호스트 풀에서 전체 데스크톱 또는 개별 원격 앱을 게시하고, 여러 사용자 세트에 대한 개별 앱 그룹을 만들고, 심지어 여러 앱 그룹에 사용자를 할당하여 이미지 수를 줄입니다.
* 환경을 관리할 때 기본 제공되는 위임된 액세스를 사용하여 역할을 할당하고 다양한 구성 또는 사용자 오류를 이해할 수 있는 진단 정보를 수집합니다.
* 새 진단 서비스를 사용하여 오류를 해결합니다.
* 인프라가 아닌 이미지와 가상 머신만 관리합니다. 원격 데스크톱 서비스에서 하듯이 원격 데스크톱 역할을 개인적으로 관리할 필요 없이 Azure 구독의 가상 머신만 관리하면 됩니다.

다음과 같이 가상 데스크톱에 사용자를 할당하고 연결할 수도 있습니다.

* 할당된 사용자는 아무 Windows Virtual Desktop 클라이언트를 실행하여 사용자를 게시된 Windows 데스크톱 및 애플리케이션에 연결할 수 있습니다. 사용 중인 디바이스의 네이티브 애플리케이션 또는 Windows Virtual Desktop HTML5 웹 클라이언트를 통해 모든 디바이스에서 연결 가능합니다.
* 역방향 연결을 통해 사용자를 서비스에 안전하게 연결하므로 인바운드 포트를 계속 열어 둘 필요가 없습니다.

## <a name="requirements"></a>요구 사항

Windows Virtual Desktop을 설정하고 Windows 데스크톱 및 애플리케이션에 사용자를 연결하려면 몇 가지 조건을 충족해야 합니다.

다음 OS에 대한 지원을 추가할 계획이므로 배포하려는 데스크톱 및 앱에 따라 사용자에게 [적절한 라이선스](https://azure.microsoft.com/pricing/details/virtual-desktop/)가 있는지 확인합니다.

|OS|필수 라이선스|
|---|---|
|Windows 10 Enterprise 다중 세션 또는 Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Software Assurance가 포함된 RDS CAL(클라이언트 액세스 라이선스)|

Windows Virtual Desktop을 지원하려면 인프라에 다음과 같은 것들이 필요합니다.

* [Azure Active Directory](/azure/active-directory/)
* Azure Active Directory와 동기화되는 Windows Server Active Directory입니다. 다음 중 하나를 사용하여 구성할 수 있습니다.
  * Azure AD Connect(하이브리드 조직용)
  * Azure AD Domain Services(하이브리드 또는 클라우드 조직용)
* Windows Server Active Directory를 포함하거나 그에 연결된 가상 네트워크를 포함하고 있는 Azure 구독
  
Windows Virtual Desktop에 대해 만드는 Azure 가상 머신은 다음과 같아야 합니다.

* [표준 도메인에 조인](../active-directory-domain-services/active-directory-ds-comparison.md) 또는 [하이브리드 AD에 조인](../active-directory/devices/hybrid-azuread-join-plan.md). 가상 머신이 Azure AD에 조인되면 안 됩니다.
* 다음 [지원되는 OS 이미지](#supported-virtual-machine-os-images) 중 하나를 실행합니다.

>[!NOTE]
>Azure 구독이 필요한 경우에는 [1개월 평가판에 가입](https://azure.microsoft.com/free/)하면 됩니다. Azure 평가판 버전을 사용하는 경우 Windows Server Active Directory와 Azure Active Directory가 동기화 상태를 유지하도록 Azure AD Domain Services를 사용해야 합니다.

Windows Virtual Desktop용으로 생성한 Azure 가상 머신에는 다음 URL에 대한 액세스 권한이 있어야 합니다.

|주소|아웃바운드 포트|목적|
|---|---|---|
|*.wvd.microsoft.com|TCP 포트 443|서비스 트래픽|
|\*.blob.core.windows.net|TCP 포트 443|에이전트, SXS 스택 업데이트 및 에이전트 트래픽|
|*.core.windows.net|TCP 포트 443|에이전트 트래픽|
|\*.servicebus.windows.net|TCP 포트 443|에이전트 트래픽|
|prod.warmpath.msftcloudes.com|TCP 포트 443|에이전트 트래픽|
|catalogartifact.azureedge.net|TCP 포트 443|Azure Marketplace|
|kms.core.windows.net|TCP 포트 1688|Windows 10 정품 인증|

>[!IMPORTANT]
>이러한 URL을 여는 것은 신뢰할 수 있는 Windows Virtual Desktop 배포에 필수적입니다. 이러한 URL에 대한 액세스를 차단하는 것은 지원되지 않으며 서비스 기능에 영향을 줍니다. 이러한 URL은 Windows Virtual Desktop 사이트 및 리소스에만 해당하고 Azure Active Directory와 같은 다른 서비스에 대한 URL은 포함하지 않습니다.

>[!NOTE]
>Windows Virtual Desktop에는 현재 네트워크 트래픽을 허용하도록 허용 목록화할 수 있는 IP 주소 범위 목록이 없습니다. 현재 특정 URL의 허용 목록만 지원합니다.
>
>서비스 트래픽과 관련된 URL에는 와일드카드 문자(*)를 사용해야 합니다. 에이전트 관련 트래픽에 *를 사용하지 않으려는 경우 와일드 카드 없이 URL을 찾는 방법은 다음과 같습니다.
>
>1. Windows Virtual Desktop 호스트 풀에 가상 머신을 등록합니다.
>2. **이벤트 뷰어**를 열고 **Windows 로그** > **애플리케이션** > **WVD-Agent**로 이동하여 이벤트 ID 3702를 찾습니다.
>3. 이벤트 ID 3702 아래에 있는 URL을 허용 목록으로 지정합니다. 이벤트 ID 3702 아래의 URL은 지역별로 다릅니다. 가상 머신을 배포하려는 각 지역에 대한 관련 URL을 사용하여 허용 목록 프로세스를 반복해야 합니다.

Windows Virtual Desktop은 고객이 사용자에게 제공하는 Windows 데스크톱과 앱, 그리고 Microsoft가 Azure에 서비스로 호스팅하는 관리 솔루션으로 구성됩니다. 데스크톱과 앱을 모든 Azure 지역의 VM(가상 머신)에 배포할 수 있으며, 이러한 VM의 관리 솔루션과 데이터는 미국에 상주합니다. 따라서 미국으로 데이터가 전송될 수 있습니다.

최적의 성능을 얻을 수 있도록 네트워크가 다음 요구 사항을 충족하는지 확인합니다.

* 클라이언트의 네트워크와 호스트 풀이 배포된 Azure 지역 간의 RTT(왕복) 대기 시간이 150밀리초 미만이어야 합니다.
* 데스크톱 및 앱을 호스팅하는 VM을 관리 서비스에 연결할 때 네트워크 트래픽이 국가/지역 경계를 벗어날 수 있습니다.
* 네트워크 성능을 최적화하기 위해 세션 호스트의 VM을 관리 서비스와 동일한 Azure 지역에 배치하는 것이 좋습니다.

## <a name="supported-remote-desktop-clients"></a>지원되는 원격 데스크톱 클라이언트

다음 원격 데스크톱 클라이언트는 Windows Virtual Desktop을 지원합니다.

* [Windows Desktop](connect-windows-7-and-10.md)
* [Web](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android(미리 보기)](connect-android.md)

> [!IMPORTANT]
> Windows Virtual Desktop은 RADC(RemoteApp 및 데스크톱 연결) 클라이언트 또는 원격 데스크톱 연결(MSTSC) 클라이언트를 지원하지 않습니다.

> [!IMPORTANT]
> Windows Virtual Desktop은 현재 Windows Store에서 원격 데스크톱 클라이언트를 지원하지 않습니다. 이 클라이언트에 대한 지원은 향후 릴리스에 추가될 예정입니다.

원격 데스크톱 클라이언트에는 다음 URL에 액세스할 수 있어야 합니다.

|주소|아웃바운드 포트|목적|클라이언트|
|---|---|---|---|
|*.wvd.microsoft.com|TCP 포트 443|서비스 트래픽|모두|
|\*.servicebus.windows.net|TCP 포트 443|데이터 문제 해결|모두|
|go.microsoft.com|TCP 포트 443|Microsoft FWLinks|모두|
|aka.ms|TCP 포트 443|Microsoft URL 단축기|모두|
|docs.microsoft.com|TCP 포트 443|문서화|모두|
|privacy.microsoft.com|TCP 포트 443|개인정보처리방침|모두|
|query.prod.cms.rt.microsoft.com|TCP 포트 443|클라이언트 업데이트|Windows Desktop|

>[!IMPORTANT]
>이러한 URL을 여는 것은 신뢰할 수 있는 클라이언트 환경을 위해 필수적입니다. 이러한 URL에 대한 액세스를 차단하는 것은 지원되지 않으며 서비스 기능에 영향을 줍니다. 이러한 URL은 클라이언트 사이트 및 리소스에만 해당하고 Azure Active Directory와 같은 다른 서비스에 대한 URL은 포함하지 않습니다.

## <a name="supported-virtual-machine-os-images"></a>지원되는 가상 머신 OS 이미지

Windows Virtual Desktop은 다음 x64 운영 체제 이미지를 지원합니다.

* Windows 10 Enterprise 다중 세션, 버전 1809 이상
* Windows 10 Enterprise, 버전 1809 이상
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows Virtual Desktop은 x86(32비트), Windows 10 Enterprise N 또는 Windows 10 Enterprise KN 운영 체제 이미지를 지원하지 않습니다. 또한 Windows 7은 섹터 크기 제한으로 인해 관리되는 Azure Storage에서 호스팅되는 VHD 또는 VHDX 기반 프로필 솔루션을 지원하지 않습니다.

사용 가능한 자동화 및 배포 옵션은 다음 표에 나와 있는 것처럼 사용자가 선택한 OS 및 버전에 따라 달라집니다. 

|운영 체제|Azure 이미지 갤러리|수동 VM 배포|Azure Resource Manager 템플릿 통합|Azure Marketplace에서 호스트 풀 프로비저닝|Windows Virtual Desktop 에이전트 업데이트|
|--------------------------------------|:------:|:------:|:------:|:------:|:------:|
|Windows 10 다중 세션, 버전 1903|예|예|예|예|자동|
|Windows 10 다중 세션, 버전 1809|예|예|예|예|자동|
|Windows 10 Enterprise, 버전 1903|예|예|예|예|자동|
|Windows 10 Enterprise, 버전 1809|예|예|예|예|자동|
|Windows 7 Enterprise|예|예|예|예|설명서|
|Windows Server 2019|예|예|예|예|자동|
|Windows Server 2016|예|예|예|예|자동|
|Windows Server 2012 R2|예|예|예|예|자동|

## <a name="next-steps"></a>다음 단계

시작하려면 테넌트를 만들어야 합니다. 테넌트를 만드는 자세한 방법을 알아보려면 테넌트 만들기 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Windows Virtual Desktop에서 테넌트 만들기](tenant-setup-azure-active-directory.md)
