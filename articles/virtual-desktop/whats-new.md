---
title: Windows Virtual Desktop의 새로운 기능 - Azure
description: Windows Virtual Desktop에 대한 새로운 기능 및 제품 업데이트.
author: Heidilohr
ms.topic: overview
ms.date: 01/06/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.custom: references_regions
ms.openlocfilehash: 2f49ec0fef5aa79c602e561746eb0f6dba16cb33
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98876600"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Windows Virtual Desktop의 새로운 기능

Windows Virtual Desktop은 정기적으로 업데이트됩니다. 이 문서에서는 다음에 대해 알아봅니다.

- 최신 업데이트
- 새로운 기능
- 기존 기능의 향상된 기능
- 버그 수정

이 문서는 매월 업데이트됩니다. 새 업데이트를 유지하려면 이 문서를 다시 확인하세요.

## <a name="december-2020"></a>2020년 12월

2020년 12월에 변경된 내용은 다음과 같습니다. 

### <a name="azure-monitor-for-windows-virtual-desktop"></a>Azure Monitor for Windows Virtual Desktop

이제 Azure Monitor for Windows Virtual Desktop용 공개 미리 보기를 사용할 수 있습니다. 이 새로운 기능에는 IT 전문가가 Windows Virtual Desktop 환경을 이해하는 데 도움을 주는 Azure Monitor 통합 문서 위에 구축된 강력한 대시보드가 포함되어 있습니다. 자세한 내용은 [블로그의 공지](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587)를 확인하세요. 

### <a name="azure-resource-manager-template-change"></a>Azure Resource Manager 템플릿 변경 

최신 업데이트에서는 호스트 풀을 만들고 프로비저닝하기 위해 Azure Resource Manager 템플릿에서 모든 공용 IP 주소 매개 변수를 제거했습니다. 배포를 안전하게 유지하기 위해 Windows Virtual Desktop에 공용 IP를 사용하지 않는 것이 좋습니다. 배포가 공용 IP에 의존하는 경우 개인 IP를 대신 사용하도록 다시 구성해야 합니다. 그렇지 않으면 배포가 제대로 작동하지 않습니다.

### <a name="msix-app-attach-public-preview"></a>MSIX 앱 연결 공개 미리 보기 

MSIX 앱 연결은 이번 달에 공개 미리 보기를 시작한 다른 서비스입니다. MSIX 앱 연결은 Windows Virtual Desktop 세션 호스트 VM에 MSIX 애플리케이션을 동적으로 제공하는 서비스입니다. 자세한 내용은 [블로그의 공지](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231)를 확인하세요. 

### <a name="screen-capture-protection"></a>화면 캡처 보호 

이번 달에는 화면 캡처 보호를 위한 공개 미리 보기의 시작도 표시되어 있습니다. 이 기능을 사용하여 클라이언트 엔드포인트에서 중요한 정보가 캡처되지 않도록 할 수 있습니다. [이 페이지](https://aka.ms/WVDScreenCaptureProtection)로 이동하여 화면 캡처 보호를 사용해 보세요.  

### <a name="built-in-roles"></a>기본 제공 역할

관리자 권한으로 Windows Virtual Desktop에 대한 새로운 기본 제공 역할을 추가했습니다. 자세한 내용은 [Windows Virtual Desktop에 대한 기본 제공 역할](rbac.md)을 참조하세요. 

### <a name="application-group-limit-increase"></a>애플리케이션 그룹 제한 증가

Azure Active Directory 테넌트당 기본 애플리케이션 그룹 제한을 200개 그룹으로 늘렸습니다.

### <a name="client-updates-for-december-2020"></a>2020년 12월 클라이언트 업데이트

다음 클라이언트의 새 버전을 릴리스했습니다. 

- Android
- macOS
- Windows

클라이언트 업데이트에 대한 자세한 내용은 [클라이언트 업데이트](whats-new.md#client-updates)를 참조하세요.

## <a name="november-2020"></a>2020년 11월

### <a name="azure-portal-experience"></a>Azure Portal 환경

Azure Portal 사용자 환경에서 다음과 같은 두 가지 버그를 해결했습니다.

- 데스크톱 애플리케이션 이름을 "VM 추가" 워크플로에서 더 이상 덮어쓰지 않습니다.
- 이제 세션 호스트가 확장 집합의 일부인 경우 세션 호스트 탭이 로드됩니다.

### <a name="fslogix-client-version-2009"></a>FSLogix 클라이언트 버전 2009 

여러 수정 및 개선 사항이 있는 새 버전의 FSLogix 클라이언트를 출시했습니다. [블로그 게시물](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix)에서 자세히 알아보세요.

### <a name="rdp-shortpath-public-preview"></a>RDP Shortpath 공개 미리 보기

RDP Shortpath는 지점 및 사이트 간과 사이트 간 VPN 및 ExpressRoute를 사용하여 Windows Virtual Desktop 세션 호스트에 직접 연결하는 것을 소개합니다. URCP 전송 프로토콜도 소개합니다. RDP Shortpath는 사용자 환경을 개선하기 위해 대기 시간과 네트워크 홉을 줄이도록 설계되었습니다. [Windows Virtual Desktop RDP Shortpath](shortpath.md)에서 자세히 알아보세요.

### <a name="azdesktopvirtualization-version-201"></a>Az.DesktopVirtualization 버전 2.0.1

Windows Virtual Desktop cmdlet 2.0.1을 출시했습니다. 이 업데이트에는 MSIX 앱 연결을 관리할 수 있는 cmdlet이 포함되어 있습니다. [PowerShell 갤러리](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1)에서 새 버전을 다운로드할 수 있습니다.

### <a name="azure-advisor-updates"></a>Azure Advisor 업데이트

이제 Azure Advisor는 Windows Virtual Desktop의 근접 지침에 대한 새로운 권장 사항과 깊이 우선 부하가 분산된 호스트 풀의 성능을 최적화하기 위한 새로운 권장 사항을 제공합니다. [Azure 웹 사이트](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/)에서 자세히 알아보세요.

## <a name="october-2020"></a>2020년 10월

2020년 10월에 변경된 내용은 다음과 같습니다.

### <a name="improved-performance"></a>성능 향상

- 다음 Azure 지역에서 연결 대기 시간을 줄여 성능을 최적화했습니다.
    - 스위스
    - 캐나다

이제 [환경 측정기](https://azure.microsoft.com/services/virtual-desktop/assessment/)를 사용하여 이러한 영역에서 사용자 환경 품질을 추정할 수 있습니다.

### <a name="azure-government-cloud-availability"></a>Azure Government 클라우드 가용성

이제 Azure Government 클라우드가 일반 공급됩니다. [블로그 게시물](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/)에서 자세히 알아보세요.

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Windows Virtual Desktop Azure Portal 업데이트

Windows Virtual Desktop Azure Portal에 대한 몇 가지 업데이트를 수행했습니다.

- 사용자가 "세션" 탭을 열 수 없도록 하는 resourceID 오류를 수정했습니다.
- "세션 호스트" 탭에서 UI를 간소화했습니다.
- RDP 속성에서 "기본값", "유용성" 및 "기본값 복원" 설정을 수정했습니다.
- 모든 탭에서 일관된 "제거" 및 "삭제" 함수를 만들었습니다.
- 이제 포털에서 "앱 추가" 워크플로의 앱 이름 유효성을 검사합니다.
- 세션 호스트 내보내기 데이터가 열에 정렬되지 않은 문제를 해결했습니다.
- 포털이 사용자 세션을 검색할 수 없는 문제를 해결했습니다.
- 다른 리소스 그룹에서 가상 머신을 생성할 때 발생하는 세션 호스트 검색 문제를 해결했습니다.
- 활성 세션과 연결이 끊긴 세션을 모든 나열하도록 "세션 호스트" 탭을 업데이트했습니다.
- 이제 "애플리케이션" 탭에 페이지가 있습니다.
- "명령줄 필요" 텍스트가 "애플리케이션 목록" 탭에 올바르게 표시되지 않는 문제를 해결했습니다.
- 독일어 버전의 공유 이미지 갤러리를 사용하는 동안 포털에서 호스트 풀 또는 가상 머신을 배포할 수 없는 문제를 해결했습니다.

### <a name="client-updates-for-october-2020"></a>2020년 10월 클라이언트 업데이트

새 버전의 클라이언트를 릴리스했습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

기타 클라이언트에 대한 자세한 내용은 [클라이언트 업데이트](#client-updates)를 참조하세요.

## <a name="september-2020"></a>2020년 9월

2020년 9월에 변경된 내용은 다음과 같습니다.

- 다음 Azure 지역에서 연결 대기 시간을 줄여 성능을 최적화했습니다.
    - 독일
    - 남아프리카(유효성 검사 환경에만 해당)

이제 [환경 측정기](https://azure.microsoft.com/services/virtual-desktop/assessment/)를 사용하여 이러한 영역에서 사용자 환경 품질을 추정할 수 있습니다.

- Windows Virtual Desktop용 Windows Desktop 클라이언트의 버전 1.2.1364를 출시했습니다. 이 업데이트에서는 다음과 같이 변경했습니다.
    - Windows 7에서 SSO(Single Sign-On)가 작동하지 않는 문제가 해결되었습니다.
    - 다른 앱에서 오디오 스트림이 전용 모드로 열려 있는 동안 팀에 대한 미디어 최적화를 사용하도록 설정한 사용자가 팀 회의를 호출하거나 조인하려고 할 때 클라이언트의 연결이 끊어지는 문제를 해결했습니다.
    - 팀에 대한 미디어 최적화를 사용하도록 설정한 경우 팀에서 오디오나 비디오 디바이스를 열거하지 않는 문제를 해결했습니다.
    - "설정과 관련하여 도움이 필요한가요?" 링크가 데스크톱 설정 페이지에 추가되었습니다.
    - 고대비 어두운 테마를 사용할 때 발생하는 "구독" 단추와 관련된 문제가 해결되었습니다.
    
- 사용자의 놀라운 도움 덕분에 Microsoft Store 원격 데스크톱 클라이언트에 대한 두 가지 중요한 문제를 해결했습니다. 클라이언트의 단계별 릴리스를 전 세계의 더 많은 사용자에게 확대함에 따라 계속해서 피드백을 검토하고 문제를 해결할 것입니다.
    
- Azure Portal에서 VM을 배포에 추가하기 위한 워크플로의 일부로 VM 위치, 이미지, 리소스 그룹, 접두사 이름, 네트워크 구성을 변경할 수 있는 새로운 기능을 추가했습니다.

- IT 전문가는 이제 Microsoft Endpoint Manager를 사용하여 하이브리드 Azure Active Directory에 조인된 Windows 10 Enterprise VM을 관리할 수 있습니다. 자세한 내용은 [블로그 게시물](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048)을 참조하세요.

## <a name="august-2020"></a>2020년 8월

2020년 8월에 변경된 내용은 다음과 같습니다.

- 다음 Azure 지역에서 연결 대기 시간을 줄이기 위해 성능을 개선했습니다. 

    - 영국
    - 프랑스
    - 노르웨이
    - 대한민국

   [경험 예측 도구](https://azure.microsoft.com/services/virtual-desktop/assessment/)를 사용하여 이러한 변경 내용이 사용자에게 미치는 영향에 대한 일반적인 개념을 파악할 수 있습니다.

- 이제 Microsoft Store 원격 데스크톱 클라이언(v10.2.1522+)를 일반적으로 사용할 수 있습니다. 이 버전의 Microsoft Store 원격 데스크톱 클라이언트는 Windows Virtual Desktop과 호환됩니다. 또한 향상된 사용자 환경을 위해 새로 고쳐진 UI 흐름을 도입했습니다. 이 업데이트에는 흐름 디자인, 밝은 모드와 어두운 모드 및 기타 많은 흥미로운 변경 내용이 포함됩니다. 또한 iOS, macOS 및 Android 클라이언트와 동일한 기본 RDP(원격 데스크톱 프로토콜) 엔진을 사용하도록 클라이언트를 다시 작성했습니다. 이를 통해 모든 플랫폼에서 새로운 기능을 더 빠르게 제공할 수 있습니다. [클라이언트를 다운로드](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab)하여 사용해 보세요!

- 클라이언트가 채팅, 채널 및 일정에서 UTC 표준 시간대만 표시하는 Teams Desktop 클라이언트(버전 1.3.00.21759)의 문제를 해결했습니다. 업데이트된 클라이언트는 이제 원격 세션의 표준 시간대를 대신 표시합니다.

- Azure Advisor는 이제 Windows Virtual Desktop의 일부입니다. Azure Portal을 통해 Windows Virtual Desktop에 액세스하면 Windows Virtual Desktop 환경을 최적화하기 위한 권장 사항을 확인할 수 있습니다. [Azure Advisor](azure-advisor.md)에서 자세히 알아보세요.

- 이제 Azure CLI는 Windows Virtual Desktop 배포를 자동화하는 데 도움이 되는 Windows Virtual Desktop(`az desktopvirtualization`)을 지원합니다. 확장 명령 목록은 [desktopvirtualization](/cli/azure/ext/desktopvirtualization/?view=azure-cli-latest&preserve-view=true)을 체크 아웃하세요.

- Windows Virtual Desktop Azure Resource Manager 인터페이스와 완벽하게 호환되도록 배포 템플릿을 업데이트했습니다. [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates)에서 템플릿을 찾을 수 있습니다.

- Windows Virtual Desktop US Gov 포털은 현재 공개 미리 보기로 제공됩니다. 자세한 내용은 [공지 사항](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/)을 참조하세요.

## <a name="july-2020"></a>2020년 7월  

7월은 Azure 리소스 관리 통합이 포함된 Windows Virtual Desktop이 일반 공급되었습니다.

이 새 릴리스에서 변경된 내용은 다음과 같습니다. 

- "2019년 가을 릴리스"는 "Windows Virtual Desktop(클래식)"이라고 하며, "2020년 봄 릴리스"는 이제 "Windows Virtual Desktop"입니다. 자세한 내용은 [이 블로그 게시물](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/)을 참조하세요. 

새 기능에 대해 자세히 알아보려면 [이 블로그 게시물](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)을 확인하세요. 

### <a name="autoscaling-tool-update"></a>자동 크기 조정 도구 업데이트

미리 보기 상태에 있던 최신 버전의 자동 크기 조정 도구는 이제 일반 공급됩니다. 이 도구는 Azure 자동화 계정 및 Azure Logic App을 사용하여 호스트 풀 내에서 세션 호스트 VM(가상 머신)을 자동으로 종료하고 다시 시작하여 인프라 비용을 절감합니다. [Azure Automation을 사용하여 세션 호스트 크기 조정](set-up-scaling-script.md)에서 자세히 알아보세요.

### <a name="azure-portal"></a>Azure portal

이제 Windows Virtual Desktop에서 Azure Portal을 사용하여 다음 작업을 수행할 수 있습니다. 

- 개인 데스크톱 세션 호스트에 직접 사용자 할당  
- 호스트 풀의 유효성 검사 환경 설정 변경 

### <a name="diagnostics"></a>진단

Log Analytics 작업 영역에 대해 미리 작성된 새로운 쿼리를 릴리스했습니다. 쿼리에 액세스하려면 **로그** 로 이동하고 **범주** 에서 **Windows Virtual Desktop** 을 선택합니다. [진단 기능에 대해 Log Analytics 사용](diagnostics-log-analytics.md)에서 자세히 알아보세요.

### <a name="update-for-remote-desktop-client-for-android"></a>Android용 원격 데스크톱 클라이언트 업데이트

이제 [Android용 원격 데스크톱 클라이언트](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx)에서 Windows Virtual Desktop 연결을 지원합니다. 10.0.7 버전부터 Android 클라이언트는 향상된 사용자 환경을 위한 새 UI를 제공합니다. 또한 클라이언트는 Windows Virtual Desktop 작업 영역을 구독할 때 조건부 액세스를 사용하도록 Android 디바이스에서 Microsoft Authenticator와 통합됩니다.  

이제 이전 버전의 원격 데스크톱 클라이언트를 "원격 데스크톱 8"이라고 합니다. 이전 버전의 클라이언트에 있는 기존 연결은 새 클라이언트로 원활하게 전송됩니다. 새 클라이언트는 iOS 및 macOS 클라이언트와 동일한 기본 RDP 코어 엔진에 다시 작성되었으며, 모든 플랫폼에서 새로운 기능의 빠른 릴리스를 제공합니다. 

### <a name="teams-update"></a>팀 업데이트

Windows Virtual Desktop에 대한 Microsoft Teams에 개선 사항이 있습니다. 가장 중요한 점은 Windows Virtual Desktop은 이제 Windows 데스크톱 클라이언트에 대한 오디오 및 비디오 최적화를 지원한다는 것입니다. 리디렉션은 통화 및 모임에서 오디오 또는 비디오를 사용할 때 사용자 간에 직접 경로를 만들어 대기 시간을 단축시킵니다. 거리를 줄이면 홉 수가 줄어들기 때문에 호출 모양과 소리가 더 부드러워집니다. [Windows Virtual Desktop에서 팀 사용](teams-on-wvd.md)에서 자세히 알아보세요.

## <a name="june-2020"></a>2020년 6월

지난달에는 Azure Resource Manager가 통합된 Windows Virtual Desktop을 미리 보기로 도입되었습니다. 이 업데이트에는 여러 흥미로운 새로운 기능들이 포함되어 있습니다. 이 Windows Virtual Desktop 버전의 새로운 기능은 다음과 같습니다.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>이제 Windows Virtual Desktop이 Azure Resource Manager와 통합되었습니다.

이제 Windows Virtual Desktop이 Azure Resource Manager에 통합되었습니다. 최신 업데이트에서는 이제 모든 Windows Virtual Desktop 개체가 Azure Resource Manager 리소스입니다. 또한 이 업데이트는 Azure RBAC(Azure 역할 기반 액세스 제어)와도 통합됩니다. 자세한 내용은 [Azure Resource Manager란?](../azure-resource-manager/management/overview.md)을 참조하세요.

이러한 변화가 미치는 영향은 다음과 같습니다.

- 이제 Windows Virtual Desktop이 Azure Portal과 통합되었습니다. 즉, 포털에서 직접 모든 항목을 관리할 수 있습니다. PowerShell, 웹앱 또는 타사 도구는 필요하지 않습니다. 시작하려면 [Azure Portal을 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)에 있는 자습서를 확인하세요.

- 이 업데이트 전에는 개별 사용자에게만 RemoteApps 및 데스크톱을 게시할 수 있었습니다. 이제 Azure Resource Manager를 사용하면 Azure Active Directory 그룹에 리소스를 게시할 수 있습니다.

- 이전 버전의 Windows Virtual Desktop에는 테넌트 또는 호스트 풀에 할당할 수 있는 네 가지 기본 제공 관리자 역할이 있습니다. 이러한 역할은 이제 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/overview.md)에 있습니다. 모든 Windows Virtual Desktop Azure Resource Manager 개체에 이러한 역할을 적용할 수 있으며, 이를 통해 완전하고 다양한 위임 모델을 사용할 수 있습니다.

- 이 업데이트에서는 더 이상 호스트 풀을 확장하기 위해 Azure Marketplace 또는 GitHub 템플릿을 반복적으로 실행할 필요가 없습니다. 호스트 풀을 확장하려면 Azure Portal의 호스트 풀로 이동하고 **+ 추가** 를 선택하여 추가 세션 호스트를 배포하기만 하면 됩니다.

- 이제 호스트 풀 배포가 [Azure 공유 이미지 갤러리](../virtual-machines/shared-image-galleries.md)와 완전히 통합되었습니다. 공유 이미지 갤러리는 이미지 버전 관리를 비롯한 VM(가상 머신) 이미지 정의를 저장하는 별도의 Azure 서비스입니다. 또한 글로벌 복제를 사용하여 로컬 배포를 위해 이미지를 복사하여 다른 Azure 지역으로 보낼 수 있습니다.

- PowerShell 또는 진단 서비스 웹앱을 통해 수행되는 함수 모니터링은 이제 Azure Portal의 Log Analytics로 이동되었습니다. 또한 이제 두 가지 옵션을 통해 보고서를 시각화할 수 있습니다. Kusto 쿼리를 실행하고 통합 문서를 사용하여 시각적 보고서를 만들 수 있습니다.

- 더 이상 Windows Virtual Desktop을 사용하도록 Azure AD(Azure Active Directory) 동의를 완료할 필요가 없습니다. 이 업데이트에서 Azure 구독의 Azure AD 테넌트는 사용자를 인증하고 관리자를 위한 Azure RBAC 제어를 제공합니다.

### <a name="powershell-support"></a>PowerShell 지원

이 업데이트를 통해 Azure PowerShell Az 모듈에 새 AzWvd cmdlet을 추가했습니다. 이 새 모듈은 .NET Core에서 실행되는 PowerShell Core에서 지원됩니다.

모듈을 설치하려면 [Windows Virtual Desktop용 PowerShell 모듈 설치](powershell-module.md)에 나온 지침을 따르세요.

[AzWvd PowerShell 참조](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization&preserve-view=true)에서 사용 가능한 명령 목록을 확인할 수도 있습니다.

새 기능에 대한 자세한 내용은 [블로그 게시물](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)을 확인하세요.

### <a name="additional-gateways"></a>추가 게이트웨이

연결 대기 시간을 줄이기 위해 새 게이트웨이 클러스터를 남아프리카 공화국에 추가했습니다.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Windows Virtual Desktop(미리 보기)의 Microsoft Teams

Windows Virtual Desktop에 대한 Microsoft Teams에 몇 가지 개선 사항이 있습니다. 가장 중요한 점은 Windows Virtual Desktop은 이제 호출에 대한 오디오 및 시각적 리디렉션을 지원한다는 것입니다. 리디렉션은 오디오 또는 비디오를 사용하여 호출할 때 사용자 간에 직접 경로를 만들어 대기 시간을 단축시킵니다. 거리를 줄이면 홉 수가 줄어들기 때문에 호출 모양과 소리가 더 부드러워집니다.

자세한 내용은 [블로그 게시물](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)을 참조하세요.

## <a name="client-updates"></a>클라이언트 업데이트

다음 문서를 확인하여 Windows Virtual Desktop 및 원격 데스크톱 서비스의 클라이언트에 대한 업데이트를 알아봅니다.

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="next-steps"></a>다음 단계

[Microsoft 365 Windows Virtual Desktop 로드맵](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)에서 향후 계획에 대해 알아봅니다.