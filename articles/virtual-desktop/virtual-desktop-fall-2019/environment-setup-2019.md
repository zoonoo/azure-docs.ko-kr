---
title: Azure Virtual Desktop(클래식) 환경 - Azure
description: Azure Virtual Desktop(클래식) 환경의 기본 요소
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9062565f5c13d7b278b57068df79c1bb4de66424
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745064"
---
# <a name="azure-virtual-desktop-classic-environment"></a>Azure Virtual Desktop(클래식) 환경

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 지원하지 않는 Azure Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager Azure Virtual Desktop 개체를 관리하려는 경우 [이 문서](../environment-setup.md)를 참조하세요.

Azure Virtual Desktop은 사용자에게 가상화된 데스크톱 및 RemoteApp에 대한 액세스를 쉽고 안전하게 제공하는 서비스입니다. 이 항목에서는 Azure Virtual Desktop 환경의 일반적인 구조에 대해 자세히 설명합니다.

## <a name="tenants"></a>테넌트

Azure Virtual Desktop 테넌트는 Azure Virtual Desktop 환경을 관리하기 위한 기본 인터페이스입니다. 각 Azure Virtual Desktop 테넌트는 환경에 로그인할 사용자를 포함하는 Azure Active Directory와 연결되어야 합니다. Azure Virtual Desktop 테넌트에서 사용자의 워크로드를 실행하는 호스트 풀 만들기를 시작할 수 있습니다.

## <a name="host-pools"></a>호스트 풀

호스트 풀은 Azure Virtual Desktop 에이전트를 실행할 때 Azure Virtual Desktop을 세션 호스트로 등록하는 Azure 가상 머신의 컬렉션입니다. 일관된 사용자 환경을 위해 호스트 풀의 모든 세션 호스트 가상 머신은 동일한 이미지에서 원본이어야 합니다.

호스트 풀은 다음 두 가지 유형 중 하나일 수 있습니다.

- 개인 - 각 세션 호스트가 개별 사용자에게 할당됩니다.
- 풀링된 - 세션 호스트가 호스트 풀 내의 앱 그룹에 대해 권한이 부여된 모든 사용자의 연결을 허용할 수 있습니다.

호스트 풀의 추가 속성을 설정하여 부하 분산 동작, 각 세션 호스트에서 수행할 수 있는 세션 수, 사용자가 해당 Azure Virtual Desktop 세션에 로그인한 동안 호스트 풀의 세션 호스트에 대해 수행할 수 있는 작업을 변경할 수 있습니다. 앱 그룹을 통해 사용자에게 게시된 리소스를 제어할 수 있습니다.

## <a name="app-groups"></a>앱 그룹

앱 그룹은 호스트 풀의 세션 호스트에 설치된 애플리케이션의 논리적 그룹입니다. 앱 그룹은 다음 두 가지 유형 중 하나일 수 있습니다.

- RemoteApp - 사용자가 앱 그룹에 개별적으로 선택하고 게시하는 Remoteapp에 액세스합니다.
- 데스크톱 - 사용자가 전체 데스크톱에 액세스합니다.

기본적으로 "데스크톱 애플리케이션 그룹" 이라는 데스크톱 앱 그룹은 호스트 풀을 만들 때마다 자동으로 생성됩니다. 언제든지 이 앱 그룹을 제거할 수 있습니다. 그러나 데스크톱 애플리케이션 그룹이 있는 동안에는 호스트 풀에서 다른 데스크톱 앱 그룹을 만들 수 없습니다. RemoteApp을 게시하려면 RemoteApp 앱 그룹을 만들어야 합니다. 여러 가지 작업자 시나리오를 수용하기 위해 여러 RemoteApp 앱 그룹을 만들 수 있습니다. 다른 RemoteApp 앱 그룹에는 겹치는 RemoteApp이 포함될 수도 있습니다.

사용자에게 리소스를 게시하려면 앱 그룹에 리소스를 할당해야 합니다. 앱 그룹에 사용자를 할당하는 경우 다음 사항을 고려합니다.

- 동일한 호스트 풀의 데스크톱 앱 그룹과 RemoteApp 앱 그룹 모두에 사용자를 할당할 수 없습니다.
- 사용자는 동일한 호스트 풀 내의 여러 앱 그룹에 할당될 수 있으며, 해당 피드는 두 앱 그룹의 누적입니다.

## <a name="tenant-groups"></a>테넌트 그룹

Azure Virtual Desktop에서 Azure Virtual Desktop 테넌트는 대부분의 설정 및 구성이 발생하는 위치입니다. Azure Virtual Desktop 테넌트에는 호스트 풀, 앱 그룹 및 앱 그룹 사용자 할당이 포함됩니다. 그러나 한 번에 여러 Azure Virtual Desktop 테넌트를 관리해야 하는 특정 상황이 있을 수 있습니다. 특히, CSP(클라우드 서비스 공급자) 또는 호스팅 파트너인 경우에는 더욱 그렇습니다. 이러한 경우 사용자 지정 Azure Virtual Desktop 테넌트 그룹을 사용하여 각 고객의 Azure Virtual Desktop 테넌트를 저장하고 중앙에서 액세스를 관리할 수 있습니다. 그러나 단일 Azure Virtual Desktop 테넌트만 관리하는 경우에는 테넌트 그룹 개념이 적용되지 않으며 기본 테넌트 그룹에 있는 테넌트를 계속 작동하고 관리할 수 있습니다.

## <a name="end-users"></a>최종 사용자

사용자를 앱 그룹에 할당한 후에는 Azure Virtual Desktop 클라이언트를 사용하여 Azure Virtual Desktop 배포에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Virtual Desktop의 위임된 액세스](delegated-access-virtual-desktop-2019.md)에서 위임된 액세스와 사용자에게 역할을 할당하는 방법에 대해 자세히 알아봅니다.

Azure Virtual Desktop 테넌트를 설정하는 방법을 알아보려면 [Azure Virtual Desktop에서 테넌트 만들기](tenant-setup-azure-active-directory.md)를 참조하세요.

Azure Virtual Desktop에 연결하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Windows 10 또는 Windows 7에서 연결](connect-windows-7-10-2019.md)
- [웹 브라우저에서 연결](connect-web-2019.md)
