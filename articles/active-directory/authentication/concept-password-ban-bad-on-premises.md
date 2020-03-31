---
title: Azure AD 암호 보호 - Azure Active Directory
description: Azure AD 암호 보호를 사용하여 온-프레미스 Active Directory에서 약한 암호 차단
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9d07099f8de996181948921330ef6744b302a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848649"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Windows Server Active Directory에 Azure AD 암호 보호 강제 적용

Azure AD 암호 보호는 조직의 암호 정책을 향상시키는 기능입니다. 온-프레미스 암호 보호 배포는 Azure AD에 저장된 전역 및 사용자 지정 금지 암호 목록을 모두 사용합니다. Azure AD가 클라우드 기반 변경에 대해 수행하는 것과 동일한 온-프레미스 검사를 수행합니다. 이러한 검사는 암호 변경 및 암호 재설정 시나리오 중에 수행됩니다.

## <a name="design-principles"></a>디자인 원칙

Azure AD 암호 보호는 다음 원칙을 염두에 두고 설계되었습니다.

* 도메인 컨트롤러는 인터넷과 직접 통신할 필요가 없습니다.
* 새 네트워크 포트가 도메인 컨트롤러에서 열려 있지 않습니다.
* Active Directory 스키마 변경이 필요하지 않습니다. 이 소프트웨어는 기존 Active Directory **컨테이너** 및 **serviceConnectionPoint** 스키마 개체를 사용합니다.
* 최소 Active Directory 도메인 또는 포리스트 기능 수준(DFL/FFL)은 필요하지 않습니다.
* 이 소프트웨어는 보호하는 Active Directory 도메인의 계정을 만들거나 요구하지 않습니다.
* 사용자 일반 텍스트 암호는 암호 유효성 검사 작업 중이거나 다른 시간에 도메인 컨트롤러를 떠나지 않습니다.
* 소프트웨어는 다른 Azure AD 기능에 종속되지 않습니다. 예를 들어 Azure AD 암호 해시 동기화는 관련이 없으며 Azure AD 암호 보호가 작동하기 위해 필요하지 않습니다.
* 증분 배포가 지원되지만 DC 에이전트(DC 에이전트)가 설치된 경우에만 암호 정책이 적용됩니다. 자세한 내용은 다음 항목을 참조하세요.

## <a name="incremental-deployment"></a>증분 배포

Azure AD 암호 보호는 Active Directory 도메인의 도메인 컨트롤러 간에 증분 배포를 지원하지만 이것이 실제로 의미하는 것과 장단점을 이해하는 것이 중요합니다.

Azure AD 암호 보호 DC 에이전트 소프트웨어는 도메인 컨트롤러에 설치된 경우에만 암호의 유효성을 검사할 수 있으며 해당 도메인 컨트롤러로 전송되는 암호 변경에 대해서만 유효성을 검사할 수 있습니다. 사용자 암호 변경을 처리하기 위해 Windows 클라이언트 컴퓨터에서 선택한 도메인 컨트롤러를 제어할 수 없습니다. 일관된 동작과 범용 암호 보호 보안 적용을 보장하려면 DC 에이전트 소프트웨어를 도메인의 모든 도메인 컨트롤러에 설치해야 합니다.

많은 조직에서는 전체 배포를 수행하기 전에 도메인 컨트롤러의 하위 집합에서 Azure AD 암호 보호를 신중하게 테스트하려고 합니다. Azure AD 암호 보호는 부분 배포를 지원하며, 즉 지정된 DC의 DC 에이전트 소프트웨어는 도메인의 다른 DC에 DC 에이전트 소프트웨어가 설치되어 있지 않은 경우에도 암호를 적극적으로 확인합니다. 이 유형의 부분 배포는 안전하지 않으며 테스트 목적 이외의 권장사항은 아닙니다.

## <a name="architectural-diagram"></a>아키텍처 다이어그램

온-프레미스 Active Directory 환경에서 Azure AD 암호 보호를 배포하기 전에 기본 디자인 및 기능 개념을 이해하는 것이 중요합니다. 다음 다이어그램에서는 암호 보호 구성 요소가 함께 작동하는 방법을 보여 줍니다.

![Azure AD 암호 보호 구성 요소가 함께 작동하는 방법](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD 암호 보호 프록시 서비스는 현재 Active Directory 포리스트의 도메인에 조인된 모든 머신에서 실행됩니다. 주요 목적은 도메인 컨트롤러에서 Azure AD로 암호 정책 다운로드 요청을 전달하는 것입니다. 그런 다음 Azure AD의 응답을 도메인 컨트롤러로 반환합니다.
* DC 에이전트의 암호 필터 DLL은 운영 체제에서 사용자 암호 유효성 검사 요청을 수신합니다. 도메인 컨트롤러에서 로컬로 실행 중인 DC 에이전트 서비스로 전달합니다.
* 암호 보호의 DC 에이전트 서비스는 DC 에이전트의 암호 필터 DLL에서 암호 유효성 검사 요청을 받습니다. 현재(로컬로 사용 가능한) 암호 정책을 사용하여 처리하고 결과를 *fail* *반환합니다.*

## <a name="how-password-protection-works"></a>암호 보호 작동 방식

각 Azure AD 암호 보호 프록시 서비스 인스턴스는 Active Directory에서 **serviceConnectionPoint** 개체를 만들어 포리스트의 도메인 컨트롤러에 자신을 보급합니다.

암호 보호를 위한 각 DC 에이전트 서비스도 Active Directory에서 **serviceConnectionPoint** 개체를 만듭니다. 이 개체는 주로 보고 및 진단에 사용됩니다.

DC 에이전트 서비스는 Azure AD에서 새 암호 정책을 다운로드하는 것을 담당합니다. 첫 번째 단계는 프록시 **서비스ConnectionPoint** 개체에 대 한 포리스트를 쿼리 하 여 Azure AD 암호 보호 프록시 프록시를 찾는 것입니다. 사용 가능한 프록시 서비스가 발견되면 DC 에이전트는 암호 정책 다운로드 요청을 프록시 서비스로 보냅니다. 프록시 서비스는 차례로 요청을 Azure AD로 보냅니다. 그런 다음 프록시 서비스가 DC 에이전트 서비스에 대한 응답을 반환합니다.

DC 에이전트 서비스가 Azure AD에서 새 암호 정책을 받은 후 서비스는 해당 도메인 *sysvol* 폴더 공유의 루트에 전용 폴더에 정책을 저장합니다. 또한 DC 에이전트 서비스는 도메인의 다른 DC 에이전트 서비스에서 새 정책이 복제되는 경우에 이 폴더를 모니터링합니다.

DC 에이전트 서비스는 항상 서비스 시작 시 새 정책을 요청합니다. DC 에이전트 서비스가 시작된 후 시간당 현재 로컬로 사용 가능한 정책의 사용 시간을 확인합니다. 정책이 1시간 이상 지난 경우 DC 에이전트는 앞서 설명한 대로 프록시 서비스를 통해 Azure AD에서 새 정책을 요청합니다. 현재 정책이 1시간 이상 지난 경우 DC 에이전트는 해당 정책을 계속 사용합니다.

Azure AD 암호 보호 암호 정책을 다운로드할 때마다 해당 정책은 테넌트에 만 적용됩니다. 즉, 암호 정책은 항상 Microsoft 전역 금지 암호 목록과 테넌트당 사용자 지정 금지 암호 목록의 조합입니다.

DC 에이전트는 TCP를 통해 RPC를 통해 프록시 서비스와 통신합니다. 프록시 서비스는 구성에 따라 동적 또는 정적 RPC 포트에서 이러한 호출을 수신 대기합니다.

DC 에이전트는 네트워크에서 사용할 수 있는 포트에서 수신하지 않습니다.

프록시 서비스는 DC 에이전트 서비스를 호출하지 않습니다.

프록시 서비스는 상태 비수입니다. Azure에서 다운로드한 정책 또는 기타 상태를 캐시하지 않습니다.

DC 에이전트 서비스는 항상 사용자의 암호를 평가하기 위해 가장 최근에 로컬로 사용 가능한 암호 정책을 사용합니다. 로컬 DC에서 암호 정책을 사용할 수 없는 경우 암호가 자동으로 허용됩니다. 이 경우 관리자에게 경고하기 위해 이벤트 메시지가 기록됩니다.

Azure AD 암호 보호는 실시간 정책 응용 프로그램 엔진이 아닙니다. Azure AD에서 암호 정책 구성을 변경하는 시점과 해당 변경이 모든 도메인 컨트롤러에 도달하고 적용되는 시점 사이에 지연이 있을 수 있습니다.

Azure AD 암호 보호는 대체가 아닌 기존 Active Directory 암호 정책에 대한 보완 기능으로 작동합니다. 여기에는 설치될 수 있는 다른 제3자 암호 필터 dlls가 포함됩니다. Active Directory는 항상 암호를 수락하기 전에 모든 암호 유효성 검사 구성 요소가 동의하도록 요구합니다.

## <a name="foresttenant-binding-for-password-protection"></a>암호 보호를 위한 포리스트/테넌트 바인딩

Active Directory 포리스트에 Azure AD 암호 보호를 배포하려면 Azure AD를 사용 하 고 해당 포리스트를 등록 해야 합니다. 배포되는 각 프록시 서비스도 Azure AD에 등록해야 합니다. 이러한 포리스트 및 프록시 등록은 등록 중에 사용되는 자격 증명에 의해 암시적으로 식별되는 특정 Azure AD 테넌트와 연결됩니다.

Active Directory 포리스트와 포리스트 내에서 배포된 모든 프록시 서비스는 동일한 테넌트에 등록되어야 합니다. Active Directory 포리스트 또는 해당 포리스트에 다른 Azure AD 테넌트에 등록 되는 프록시 서비스를 포함 하는 지원 되지 않습니다. 이러한 잘못 구성된 배포의 증상으로는 암호 정책을 다운로드할 수 없습니다.

## <a name="download"></a>다운로드

Azure AD 암호 보호를 위해 필요한 두 에이전트 설치 관리자는 [Microsoft 다운로드 센터에서](https://www.microsoft.com/download/details.aspx?id=57071)사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure AD 암호 보호 배포](howto-password-ban-bad-on-premises-deploy.md)
