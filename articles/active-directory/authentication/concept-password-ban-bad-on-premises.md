---
title: Azure AD 암호 보호-Azure Active Directory
description: Azure AD 암호 보호를 사용 하 여 온-프레미스 Active Directory Domain Services 환경에서 weak 암호 금지
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: feb7c4a4417d64e039793bd96141c965f6437414
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050924"
---
# <a name="enforce-on-premises-azure-ad-password-protection-for-active-directory-domain-services"></a>Active Directory Domain Services에 대 한 온-프레미스 Azure AD 암호 보호 적용

Azure AD 암호 보호는 알려진 취약 한 암호와 해당 변형을 검색 하 고 차단 하며, 조직과 관련 된 추가 약한 용어를 차단할 수도 있습니다. Azure ad 암호 보호의 온-프레미스 배포는 Azure AD에 저장 된 것과 동일한 글로벌 및 사용자 지정 금지 된 암호 목록을 사용 하며, 클라우드 기반 변경에 대해 Azure AD에서 수행 하는 것과 동일한 온-프레미스 암호 변경 사항을 확인 합니다. 이러한 검사는 온-프레미스 Active Directory Domain Services (AD DS) 도메인 컨트롤러에 대 한 암호 변경 및 암호 재설정 이벤트 중에 수행 됩니다.

## <a name="design-principles"></a>디자인 원칙

Azure AD 암호 보호는 다음과 같은 원칙을 염두에 두어야 합니다.

* Dc (도메인 컨트롤러)는 인터넷과 직접 통신할 필요가 없습니다.
* 새 네트워크 포트가 Dc에서 열려 있지 않습니다.
* AD DS 스키마를 변경할 필요가 없습니다. 이 소프트웨어는 기존 AD DS *컨테이너* 및 *serviceConnectionPoint* 스키마 개체를 사용 합니다.
* 최소 AD DS 도메인 또는 포리스트 기능 수준 (DFL/FFL)이 필요 하지 않습니다.
* 소프트웨어는 보호 하는 AD DS 도메인에서 계정을 만들거나 요구 하지 않습니다.
* 사용자 일반 텍스트 암호는 암호 유효성 검사 작업 중 또는 다른 시간에 도메인 컨트롤러를 떠나지 않습니다.
* 이 소프트웨어는 다른 Azure AD 기능에 종속 되지 않습니다. 예를 들어 azure ad 암호 보호에는 Azure AD PHS (암호 해시 동기화)가 관련 되지 않거나 필요 하지 않습니다.
* 증분 배포를 지원 하지만 암호 정책은 도메인 컨트롤러 에이전트 (DC 에이전트)가 설치 된 위치에만 적용 됩니다.

## <a name="incremental-deployment"></a>증분 배포

Azure AD 암호 보호는 AD DS 도메인의 Dc에서 증분 배포를 지원 합니다. 이에 대 한 의미와 장단점을 이해 하는 것이 중요 합니다.

Azure AD 암호 보호 DC 에이전트 소프트웨어는 DC에 설치 된 경우 및 해당 DC로 전송 되는 암호 변경에 대해서만 암호의 유효성을 검사할 수 있습니다. 사용자 암호 변경을 처리 하기 위해 Windows 클라이언트 컴퓨터에서 선택 하는 Dc를 제어할 수 없습니다. 일관성 있는 동작 및 범용 Azure AD 암호 보호 보안 적용을 보장 하려면 DC 에이전트 소프트웨어를 도메인의 모든 Dc에 설치 해야 합니다.

대부분의 조직에서는 전체 배포 전에 해당 Dc의 하위 집합에서 Azure AD 암호 보호를 신중 하 게 테스트 하려고 합니다. 이 시나리오를 지원 하기 위해 Azure AD 암호 보호는 부분 배포를 지원 합니다. 지정 된 DC의 DC 에이전트 소프트웨어는 도메인의 다른 Dc에 DC 에이전트 소프트웨어가 설치 되어 있지 않은 경우에도 암호의 유효성을 검사 합니다. 이 형식의 부분 배포는 안전 하지 않으며 테스트 목적으로는 사용 하지 않는 것이 좋습니다.

## <a name="architectural-diagram"></a>아키텍처 다이어그램

온-프레미스 AD DS 환경에서 Azure AD 암호 보호를 배포 하기 전에 기본적인 디자인 및 기능 개념을 이해 하는 것이 중요 합니다. 다음 다이어그램은 Azure AD 암호 보호 구성 요소가 함께 작동하는 방법을 보여줍니다.

![Azure AD 암호 보호 구성 요소가 함께 작동하는 방식](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD 암호 보호 프록시 서비스는 현재 AD DS 포리스트의 모든 도메인에 가입 된 컴퓨터에서 실행 됩니다. 서비스의 기본 목적은 Dc에서 Azure AD로 암호 정책 다운로드 요청을 전달 하 고 Azure AD에서 DC로 응답을 반환 하는 것입니다.
* DC 에이전트의 암호 필터 DLL은 운영 체제에서 사용자 암호 유효성 검사 요청을 받습니다. 이 필터는 DC에서 로컬로 실행 되는 DC 에이전트 서비스로이를 전달 합니다.
* Azure AD 암호 보호의 DC 에이전트 서비스는 DC 에이전트의 암호 필터 DLL에서 암호 유효성 검사 요청을 받습니다. DC 에이전트 서비스는 현재 (로컬로 사용 가능한) 암호 정책을 사용 하 여이를 처리 하 고 *성공* 또는 *실패*결과를 반환 합니다.

## <a name="how-azure-ad-password-protection-works"></a>Azure AD 암호 보호 작동 방법

온-프레미스 Azure AD 암호 보호 구성 요소는 다음과 같이 작동 합니다.

1. 각 Azure AD 암호 보호 프록시 서비스 인스턴스는 Active Directory에서 *serviceConnectionPoint* 개체를 만들어 포리스트의 dc에 자신을 알립니다.

    Azure AD 암호 보호를 위한 각 DC 에이전트 서비스는 또한 Active Directory에 *serviceConnectionPoint* 개체를 만듭니다. 이 개체는 주로 보고 및 진단에 사용 됩니다.

1. DC 에이전트 서비스는 Azure AD에서 새 암호 정책의 다운로드를 시작 하는 일을 담당 합니다. 첫 번째 단계는 프록시 *serviceConnectionPoint* 개체에 대해 포리스트를 쿼리하여 Azure AD 암호 보호 프록시 서비스를 찾는 것입니다.

1. 사용 가능한 프록시 서비스가 있으면 DC 에이전트는 프록시 서비스에 암호 정책 다운로드 요청을 보냅니다. 그러면 프록시 서비스가 Azure AD로 요청을 보낸 다음 DC 에이전트 서비스에 대 한 응답을 반환 합니다.

1. DC 에이전트 서비스가 Azure AD에서 새 암호 정책을 받은 후 서비스는 해당 도메인 *sysvol* 폴더 공유의 루트에 있는 전용 폴더에 정책을 저장 합니다. 또한 DC 에이전트 서비스는 새 정책이 도메인의 다른 DC 에이전트 서비스에서 복제 되는 경우이 폴더를 모니터링 합니다.

1. DC 에이전트 서비스는 서비스를 시작할 때 항상 새 정책을 요청 합니다. DC 에이전트 서비스가 시작 된 후에는 현재 로컬로 사용 가능한 정책의 기간을 매시간 확인 합니다. 정책이 1 시간 보다 오래 된 경우 DC 에이전트는 앞에서 설명한 대로 프록시 서비스를 통해 Azure AD에서 새 정책을 요청 합니다. 현재 정책이 1 시간 보다 오래 되지 않은 경우 DC 에이전트는 해당 정책을 계속 사용 합니다.

1. DC에서 암호 변경 이벤트를 수신 하면 캐시 된 정책을 사용 하 여 새 암호를 수락 하거나 거부할지를 결정 합니다.

### <a name="key-considerations-and-features"></a>주요 고려 사항 및 기능

* Azure AD 암호 보호 암호 정책이 다운로드 될 때마다 해당 정책은 테 넌 트에만 적용 됩니다. 즉, 암호 정책은 항상 Microsoft 전역 금지 된 암호 목록과 테 넌 트 당 사용자 지정 금지 된 암호 목록을 조합한 것입니다.
* DC 에이전트는 RPC over TCP를 통해 프록시 서비스와 통신 합니다. 프록시 서비스는 구성에 따라 동적 또는 정적 RPC 포트에서 이러한 호출을 수신 합니다.
* DC 에이전트는 네트워크에서 사용 가능한 포트에서 수신 대기 하지 않습니다.
* 프록시 서비스는 DC 에이전트 서비스를 호출 하지 않습니다.
* 프록시 서비스는 상태 비저장입니다. Azure에서 다운로드 한 정책 또는 다른 상태는 캐시 하지 않습니다.
* DC 에이전트 서비스는 항상 가장 최근의 로컬 사용 가능한 암호 정책을 사용 하 여 사용자의 암호를 평가 합니다. 로컬 DC에서 사용할 수 있는 암호 정책이 없으면 암호가 자동으로 허용 됩니다. 이 경우 관리자에 게 경고 하는 이벤트 메시지가 기록 됩니다.
* Azure AD 암호 보호는 실시간 정책 응용 프로그램 엔진이 아닙니다. Azure AD에서 암호 정책 구성이 변경 되는 시점 및 해당 변경이 모든 Dc에서 적용 되 고 적용 되는 시점 사이에 지연이 있을 수 있습니다.
* Azure AD 암호 보호는 교체가 아닌 기존 AD DS 암호 정책에 대 한 보완 역할을 합니다. 여기에는 설치할 수 있는 타사 암호 필터 dll이 포함 됩니다. AD DS 암호를 수락 하기 전에 항상 모든 암호 유효성 검사 구성 요소에 동의 해야 합니다.

## <a name="forest--tenant-binding-for-azure-ad-password-protection"></a>Azure AD 암호 보호를 위한 포리스트/테 넌 트 바인딩

AD DS 포리스트에 Azure AD 암호 보호를 배포 하려면 Azure AD에 해당 포리스트를 등록 해야 합니다. 또한 배포 된 각 프록시 서비스는 Azure AD에 등록 되어야 합니다. 이러한 포리스트 및 프록시 등록은 등록 중에 사용 되는 자격 증명으로 암시적으로 식별 되는 특정 Azure AD 테 넌 트와 연결 됩니다.

포리스트 내의 AD DS 포리스트와 모든 배포 된 프록시 서비스는 동일한 테 넌 트에 등록 되어야 합니다. 다른 Azure AD 테 넌 트에 등록 되는 AD DS 포리스트 또는 해당 포리스트의 프록시 서비스는 지원 되지 않습니다. 잘못 구성 된 배포의 증상으로는 암호 정책을 다운로드할 수 없습니다.

## <a name="download"></a>다운로드

[Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=57071)에서 Azure AD 암호 보호에 필요한 두 개의 에이전트 설치 관리자를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

온-프레미스 Azure AD 암호 보호 사용을 시작 하려면 다음 방법을 완료 하세요.

> [!div class="nextstepaction"]
> [온-프레미스 Azure AD 암호 보호 배포](howto-password-ban-bad-on-premises-deploy.md)
