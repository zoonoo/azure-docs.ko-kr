---
title: Azure AD 암호 보호 - Azure Active Directory
description: Azure AD 암호 보호를 사용하여 온-프레미스 Active Directory Domain Services 환경에서 취약한 암호 금지
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5df1cb158821fb0cd85d90f9ba3b79d80adf45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96743924"
---
# <a name="enforce-on-premises-azure-ad-password-protection-for-active-directory-domain-services"></a>Active Directory Domain Services에 온-프레미스 Azure AD 암호 보호 적용

Azure AD 암호 보호는 알려진 취약한 암호와 관련 변형을 검색하고 차단하며 조직에 특정한 취약한 조건도 추가로 차단할 수 있습니다. Azure AD 암호 보호의 온-프레미스 배포는 Azure AD에 저장된 것과 동일한 전역 및 사용자 지정 금지 암호 목록을 사용하며, Azure AD가 클라우드 기반 변경에 대해 수행하는 것과 동일한 온-프레미스 암호 변경에 대한 검사를 수행합니다. 온-프레미스 AD DS(Active Directory Domain Services) 도메인 컨트롤러에 대한 암호 변경 및 암호 재설정 이벤트 중에 이 검사를 수행합니다.

## <a name="design-principles"></a>디자인 원칙

Azure AD 암호 보호는 다음 원칙을 고려하여 설계되었습니다.

* DC(도메인 컨트롤러)는 인터넷과 직접 통신할 필요가 없습니다.
* 새 네트워크 포트는 DC에서 열리지 않습니다.
* AD DS 스키마를 변경할 필요가 없습니다. 소프트웨어는 기존 AD DS *container* 및 *serviceConnectionPoint* 스키마 개체를 사용합니다.
* 최소 AD DS 도메인 또는 포리스트 기능 수준(DFL/FFL)이 필요하지 않습니다.
* 소프트웨어는 보호하는 AD DS 도메인에서 계정을 만들거나 요구하지 않습니다.
* 사용자 일반 텍스트 암호는 암호 유효성 검사 작업 중이나 다른 시간에 도메인 컨트롤러를 떠나지 않습니다.
* 소프트웨어는 다른 Azure AD 기능에 종속되지 않습니다. 예를 들어, Azure AD 암호 보호에는 Azure AD PHS(암호 해시 동기화)가 관련되지 않거나 필요하지 않습니다.
* 증분 배포가 지원되지만 암호 정책은 DC 에이전트(도메인 컨트롤러 에이전트)가 설치된 위치에만 적용됩니다.

## <a name="incremental-deployment"></a>증분 배포

Azure AD 암호 보호는 AD DS 도메인의 DC 간 증분 배포를 지원합니다. 이 지원의 실제 의미와 절충의 개념을 이해해야 합니다.

Azure AD 암호 보호 DC 에이전트 소프트웨어는 DC에 설치된 경우에만, 해당 DC로 전송되는 암호 변경에 대해서만 암호의 유효성을 검사할 수 있습니다. 사용자 암호 변경을 처리하기 위해 Windows 클라이언트 머신에서 선택되는 DC를 제어할 수는 없습니다. 일관된 동작과 유니버설 Azure AD 암호 보호 보안 적용을 보장하려면 DC 에이전트 소프트웨어를 도메인의 모든 DC에 설치해야 합니다.

대부분의 조직에서는 전체 배포 전에 해당 DC의 하위 집합에서 Azure AD 암호 보호를 신중하게 테스트해야 합니다. 이 시나리오를 지원하기 위해 Azure AD 암호 보호는 부분 배포를 지원합니다. 지정된 DC의 DC 에이전트 소프트웨어는 도메인의 다른 DC에 DC 에이전트 소프트웨어가 설치되지 않은 경우에도 적극적으로 암호의 유효성을 검사합니다. 이 형식의 부분 배포는 안전하지 않으며 테스트 용도 이외에는 권장되지 않습니다.

## <a name="architectural-diagram"></a>아키텍처 다이어그램

온-프레미스 AD DS 환경에서 Azure AD 암호 보호를 배포하기 전에 기본 디자인 및 기능 개념을 이해하는 것이 중요합니다. 다음 다이어그램은 Azure AD 암호 보호 구성 요소가 함께 작동하는 방법을 보여줍니다.

![Azure AD 암호 보호 구성 요소가 함께 작동하는 방식](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD 암호 보호 프록시 서비스는 현재 AD DS 포리스트의 도메인에 조인된 모든 머신에서 실행됩니다. 서비스의 주 용도는 DC에서 Azure AD로 암호 정책 다운로드 요청을 전달하고 Azure AD에서 DC로 응답을 반환하는 것입니다.
* DC 에이전트의 암호 필터 DLL은 운영 체제에서 사용자 암호 유효성 검사 요청을 받습니다. 이 필터는 DC에서 로컬로 실행되는 DC 에이전트 서비스에 요청을 전달합니다.
* Azure AD 암호 보호의 DC 에이전트 서비스는 DC 에이전트의 암호 필터 DLL에서 암호 유효성 검사 요청을 받습니다. DC 에이전트 서비스는 현재(로컬로 사용 가능한) 암호 정책을 사용하여 요청을 처리하고 ‘성공’ 또는 ‘실패’의 결과를 반환합니다. 

## <a name="how-azure-ad-password-protection-works"></a>Azure AD 암호 보호의 작동 방식

온-프레미스 Azure AD 암호 보호 구성 요소는 다음과 같이 작동합니다.

1. 각 Azure AD 암호 보호 프록시 서비스 인스턴스는 Active Directory에서 *serviceConnectionPoint* 개체를 만들어 자신을 포리스트의 DC에 알립니다.

    Azure AD 암호 보호를 위한 각 DC 에이전트 서비스도 Active Directory에 *serviceConnectionPoint* 개체를 생성합니다. 해당 개체는 주로 보고 및 진단에 사용됩니다.

1. DC 에이전트 서비스는 Azure AD에서 새 암호 정책 다운로드를 시작하는 작업을 담당합니다. 첫 번째 단계는 프록시 *serviceConnectionPoint* 개체에 대한 포리스트를 쿼리하여 Azure AD 암호 보호 프록시 서비스를 찾는 것입니다.

1. 사용 가능한 프록시 서비스가 있으면 DC 에이전트는 프록시 서비스에 암호 정책 다운로드 요청을 보냅니다. 그러면 프록시 서비스가 Azure AD로 요청을 보낸 다음, DC 에이전트 서비스에 대한 응답을 반환합니다.

1. DC 에이전트 서비스는 Azure AD에서 새 암호 정책을 받은 후 도메인 *sysvol* 폴더 공유의 루트에 있는 전용 폴더에 정책을 저장합니다. 또한 DC 에이전트 서비스는 도메인의 다른 DC 에이전트 서비스에서 최신 정책이 복제되는 경우 해당 폴더를 모니터링합니다.

1. DC 에이전트 서비스는 서비스를 시작할 때 항상 새 정책을 요청합니다. DC 에이전트 서비스가 시작된 후에는 현재 로컬로 사용 가능한 정책 기간을 매시간 확인합니다. 정책이 1시간보다 오래된 경우 DC 에이전트는 앞에서 설명한 대로 프록시 서비스를 통해 Azure AD에서 새 정책을 요청합니다. 현재 정책이 1시간보다 오래되지 않으면 DC 에이전트는 해당 정책을 계속 사용합니다.

1. DC가 암호 변경 이벤트를 수신하면 캐시된 정책을 사용하여 새 암호를 수락하거나 거부할지 여부를 결정합니다.

### <a name="key-considerations-and-features"></a>주요 고려 사항 및 기능

* Azure AD 암호 보호 암호 정책이 다운로드될 때마다 해당 정책은 테넌트에만 적용됩니다. 즉, 암호 정책은 항상 Microsoft 전역 금지 암호 목록과 테넌트별 사용자 지정 금지 암호 목록을 조합한 것입니다.
* DC 에이전트는 RPC over TCP를 통해 프록시 서비스와 통신합니다. 프록시 서비스는 구성에 따라 동적 또는 정적 RPC 포트에서 이 호출을 수신 대기합니다.
* DC 에이전트는 네트워크에서 사용 가능한 포트에서 수신 대기하지 않습니다.
* 프록시 서비스는 DC 에이전트 서비스를 호출하지 않습니다.
* 프록시 서비스는 상태 비저장입니다. Azure에서 다운로드한 정책 또는 다른 상태는 캐시하지 않습니다.
* DC 에이전트 서비스는 항상 가장 최근 로컬로 사용 가능한 암호 정책을 사용하여 사용자의 암호를 평가합니다. 로컬 DC에서 암호 정책을 사용할 수 있는 경우 암호가 자동으로 수락됩니다. 이 경우 관리자에게 경고하는 이벤트 메시지가 로그됩니다.
* Azure AD 암호 보호는 실시간 정책 애플리케이션 엔진이 아닙니다. 암호 정책 구성이 Azure AD에서 변경된 시기와 해당 변경이 모든 DC에 도달하고 모든 DC에서 적용된 시기 간에 지연이 있을 수 있습니다.
* Azure AD 암호 보호는 교체가 아니라 기존 AD DS 암호 정책을 보완합니다. 여기에는 설치할 수 있는 다른 타사 암호 필터 DLL이 포함됩니다. AD DS는 항상 암호를 수락하기 전에 모든 암호 유효성 검사 구성 요소가 일치하도록 요구합니다.

## <a name="forest--tenant-binding-for-azure-ad-password-protection"></a>Azure AD 암호 보호에 대한 포리스트/테넌트 바인딩

AD DS 포리스트에 Azure AD 암호 보호를 배포하려면 Azure AD에 해당 포리스트를 등록해야 합니다. 배포된 각 프록시 서비스는 Azure AD에도 등록해야 합니다. 이 포리스트 및 프록시 등록은 등록 중에 사용되는 자격 증명으로 암시적으로 식별되는 특정 Azure AD 테넌트와 연결됩니다.

AD DS 포리스트와 포리스트 내에 배포된 모든 프록시 서비스는 동일한 테넌트에 등록되어야 합니다. AD DS 포리스트나 해당 포리스트의 모든 프록시 서비스를 다른 Azure AD 테넌트에 등록할 수는 없습니다. 배포가 잘못 구성되면 암호 정책을 다운로드할 수 없는 문제가 나타납니다.

> [!NOTE]
> 따라서 여러 Azure AD 테넌트가 있는 고객은 Azure AD 암호 보호를 위해 각 포리스트를 등록할 하나의 고유 테넌트를 선택해야 합니다.

## <a name="download"></a>다운로드

Azure AD 암호 보호에 필요한 두 가지 에이전트 설치 프로그램은 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=57071)에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

온-프레미스 Azure AD 암호 보호 사용을 시작하려면 다음 방법을 완료합니다.

> [!div class="nextstepaction"]
> [온-프레미스 Azure AD 암호 보호 배포](howto-password-ban-bad-on-premises-deploy.md)
