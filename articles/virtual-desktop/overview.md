---
title: Windows Virtual Desktop Preview란?  - Azure
description: Windows Virtual Desktop Preview의 개요입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 820c89ce352db772f629a99a438ed86448af02fe
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65965724"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>Windows Virtual Desktop Preview란? 

현재 공개 미리 보기로 제공되는 Windows Virtual Desktop Preview는 클라우드에서 실행되는 데스크톱 및 애플리케이션 가상화 서비스입니다.

Azure에서 Windows Virtual Desktop을 실행하면 다음과 같은 작업이 가능합니다.

* 확장 가능한 완전한 Windows 10을 제공하는 다중 세션 Windows 10 배포 설정
* Office 365 ProPlus를 가상화하고 다중 사용자 가상 시나리오에서 실행되도록 최적화
* Windows 7 가상 데스크톱에 무료 기간 연장 보안 업데이트 제공
* 기존 RDS(원격 데스크톱 서비스)와 Windows Server 데스크톱 및 앱을 컴퓨터로 가져오기
* 데스크톱 및 앱 가상화
* 통합 관리 환경을 사용하여 Windows 10/Windows Server/Windows 7 데스크톱 및 앱 관리

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

* 할당된 사용자는 아무 Windows Virtual Desktop 클라이언트를 실행하여 사용자를 게시된 Windows 데스크톱 및 애플리케이션에 연결할 수 있습니다. 아무 디바이스에서 디바이스의 네이티브 애플리케이션 또는 Windows Virtual Desktop HTML5 웹 클라이언트를 통해 연결합니다.
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

* [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* Azure Active Directory와 동기화되는 Windows Server Active Directory입니다. 다음을 통해 설정할 수 있습니다.
  * Azure AD Connect
  * Azure AD Domain Services
* Windows Server Active Directory를 포함하거나 그에 연결된 가상 네트워크를 포함하고 있는 Azure 구독
  
Windows Virtual Desktop에 대해 만드는 Azure 가상 머신은 다음과 같아야 합니다.

* [표준 도메인에 조인](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) 또는 [하이브리드 AD에 조인](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). 가상 머신이 Azure AD에 조인되면 안 됩니다.
* 다음 [지원되는 OS 이미지](#supported-virtual-machine-os-image) 중 하나를 실행합니다.

>[!NOTE]
>Azure 구독이 필요한 분들은 [1개월 평가판에 가입](https://azure.microsoft.com/free/)하시면 됩니다. Azure 평가판 버전을 사용하는 경우 Windows Server Active Directory와 Azure Active Directory가 동기화 상태를 유지하도록 Azure AD Domain Services를 사용해야 합니다.

Windows Virtual Desktop은 고객이 사용자에게 제공하는 Windows 데스크톱과 앱, 그리고 Microsoft가 Azure에 서비스로 호스팅하는 관리 솔루션으로 구성됩니다. 공개 미리 보기 기간에는 데스크톱과 앱을 모든 Azure 지역의 VM(가상 머신)에 배포할 수 있으며, 이러한 VM의 관리 솔루션과 데이터는 미국(미국 동부 2 지역의)에 상주합니다. 따라서 공개 미리 보기 기간에는 서비스를 테스트할 때 데이터가 미국으로 전송됩니다. 서비스가 일반 공급되는 시점부터 관리 솔루션 및 데이터 지역화의 범위를 모든 Azure 지역으로 확장하기 시작할 예정입니다.

최적의 성능을 얻을 수 있도록 네트워크가 다음 요구 사항을 충족하는지 확인합니다.

* 클라이언트의 네트워크와 호스트 풀이 배포된 Azure 지역 간의 RTT(왕복) 대기 시간이 150밀리초 미만이어야 합니다.
* 데스크톱 및 앱을 호스트하는 VM이 관리 서비스에 연결할 때 네트워크 트래픽이 국가/지역 경계 외부에서 흐를 수 있습니다.
* 네트워크 성능을 최적화하기 위해 세션 호스트의 VM을 관리 서비스와 동일한 Azure 지역에 배치하는 것이 좋습니다.

## <a name="supported-remote-desktop-clients"></a>지원되는 원격 데스크톱 클라이언트

다음 원격 데스크톱 클라이언트는 Windows Virtual Desktop을 지원합니다.

* [Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-windows-7-and-10)
* [HTML5](https://docs.microsoft.com/azure/virtual-desktop/connect-web)


## <a name="supported-virtual-machine-os-image"></a>지원되는 가상 머신 OS 이미지

Windows Virtual Desktop은 다음 OS 이미지를 지원합니다.

* Windows 10 Enterprise 다중 세션
* Windows Server 2016

## <a name="provide-feedback"></a>피드백 제공

[Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Windows Virtual Desktop 서비스에 대해 토론해 보세요. Windows Virtual Desktop이 미리 보기로 제공되는 기간에는 지원 사례를 접수하지 않습니다.

## <a name="next-steps"></a>다음 단계

시작하려면 테넌트를 만들어야 합니다. 테넌트를 만드는 자세한 방법을 알아보려면 테넌트 만들기 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Windows Virtual Desktop Preview에서 테넌트 만들기](tenant-setup-azure-active-directory.md)
