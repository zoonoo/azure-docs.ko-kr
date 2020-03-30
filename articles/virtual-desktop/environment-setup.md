---
title: 윈도우 가상 데스크톱 환경 - Azure
description: Windows 가상 데스크톱 환경의 기본 요소입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 33d058f028b7032f296ffcf82f0e5fe2c993e6fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127911"
---
# <a name="windows-virtual-desktop-environment"></a>Windows Virtual Desktop 환경

Windows 가상 데스크톱은 사용자에게 가상화된 데스크톱 및 RemoteApps에 쉽고 안전하게 액세스할 수 있는 서비스입니다. 이 항목에서는 Windows 가상 데스크톱 환경의 일반적인 구조에 대해 자세히 알려드립니다.

## <a name="tenants"></a>테넌트

Windows 가상 데스크톱 테넌트는 Windows 가상 데스크톱 환경을 관리하기 위한 기본 인터페이스입니다. 각 Windows 가상 데스크톱 테넌트는 환경에 로그인할 사용자를 포함하는 Azure Active Directory와 연결되어야 합니다. Windows 가상 데스크톱 테넌트에서 호스트 풀을 만들어 사용자의 워크로드를 실행할 수 있습니다.

## <a name="host-pools"></a>호스트 풀

호스트 풀은 Windows 가상 데스크톱 에이전트를 실행할 때 세션 호스트로 Windows 가상 데스크톱에 등록하는 Azure 가상 컴퓨터의 모음입니다. 일관된 사용자 환경을 위해 호스트 풀의 모든 세션 호스트 가상 시스템은 동일한 이미지에서 보호되어야 합니다.

호스트 풀은 다음 두 가지 유형 중 하나일 수 있습니다.

- 각 세션 호스트가 개별 사용자에게 할당되는 개인 입니다.
- 풀런(Pooled)으로, 세션 호스트는 호스트 풀 내의 앱 그룹에 권한이 부여된 모든 사용자로부터 연결을 수락할 수 있습니다.

호스트 풀에 추가 속성을 설정하여 로드 밸런싱 동작, 각 세션 호스트가 취할 수 있는 세션 수, Windows Virtual Desktop 세션에 로그인한 상태에서 호스트 풀의 세션 호스트에 대해 사용자가 수행할 수 있는 작업을 변경할 수 있습니다. 앱 그룹을 통해 사용자에게 게시된 리소스를 제어합니다.

## <a name="app-groups"></a>앱 그룹

앱 그룹은 호스트 풀의 세션 호스트에 설치된 응용 프로그램의 논리적 그룹입니다. 앱 그룹은 다음 두 가지 유형 중 하나일 수 있습니다.

- 사용자가 개별적으로 선택하고 앱 그룹에 게시하는 RemoteApp
- 사용자가 전체 데스크톱에 액세스하는 데스크톱

기본적으로 호스트 풀을 만들 때마다 데스크톱 앱 그룹("데스크톱 응용 프로그램 그룹")이 자동으로 만들어집니다. 언제든지 이 앱 그룹을 제거할 수 있습니다. 그러나 데스크톱 앱 그룹이 있는 동안에는 호스트 풀에서 다른 데스크톱 앱 그룹을 만들 수 없습니다. RemoteApps를 게시하려면 RemoteApp 앱 그룹을 만들어야 합니다. 여러 RemoteApp 앱 그룹을 만들어 다양한 작업자 시나리오를 수용할 수 있습니다. 다른 RemoteApp 앱 그룹에겹이 겹치는 RemoteApps를 포함할 수도 있습니다.

사용자에게 리소스를 게시하려면 앱 그룹에 리소스를 할당해야 합니다. 사용자를 앱 그룹에 할당할 때 다음 사항을 고려하십시오.

- 사용자는 동일한 호스트 풀에서 데스크톱 앱 그룹과 RemoteApp 앱 그룹에 모두 할당할 수 없습니다.
- 사용자는 동일한 호스트 풀 내의 여러 앱 그룹에 할당될 수 있으며 피드는 두 앱 그룹의 누적이 됩니다.

## <a name="tenant-groups"></a>테넌트 그룹

Windows 가상 데스크톱에서 Windows 가상 데스크톱 테넌트는 대부분의 설정 및 구성이 발생하는 곳입니다. Windows 가상 데스크톱 테넌트에는 호스트 풀, 앱 그룹 및 앱 그룹 사용자 할당이 포함됩니다. 그러나 특히 CSP(클라우드 서비스 공급자) 또는 호스팅 파트너인 경우 여러 Windows 가상 데스크톱 테넌트를 한 번에 관리해야 하는 특정 상황이 있을 수 있습니다. 이러한 상황에서는 사용자 지정 Windows 가상 데스크톱 테넌트 그룹을 사용하여 고객의 각 Windows 가상 데스크톱 테넌트를 배치하고 중앙에서 액세스를 관리할 수 있습니다. 그러나 단일 Windows Virtual Desktop 테넌트만 관리하는 경우 테넌트 그룹 개념이 적용되지 않으며 기본 테넌트 그룹에 있는 테넌트를 계속 작동하고 관리할 수 있습니다.

## <a name="end-users"></a>최종 사용자

사용자를 앱 그룹에 할당한 후 Windows 가상 데스크톱 클라이언트와 함께 Windows 가상 데스크톱 배포에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

위임된 액세스 및 [Windows 가상 데스크톱의 위임된 액세스에서](delegated-access-virtual-desktop.md)사용자에게 역할을 할당하는 방법에 대해 자세히 알아봅니다.

Windows 가상 데스크톱 테넌트를 설정하는 방법을 알아보려면 [Windows 가상 데스크톱에서 테넌트 만들기를](tenant-setup-azure-active-directory.md)참조하십시오.

Windows 가상 데스크톱에 연결하는 방법을 알아보려면 다음 문서 중 하나를 참조하십시오.

- [Windows 10 또는 Windows 7에서 연결](connect-windows-7-and-10.md)
- [웹 브라우저에서 연결](connect-web.md)
