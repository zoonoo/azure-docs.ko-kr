---
title: Azure AD 암호 보호
description: Azure AD 암호 보호를 사용 하 여 온-프레미스 Active Directory에서 약한 암호를 차단
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1b3660d256e4beda948f723035aa75ca8a9ed2e
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58284871"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Windows Server Active Directory에 Azure AD 암호 보호 강제 적용

Azure AD 암호 보호 기능은 조직에서 암호 정책을 강화 합니다. 온-프레미스 배포 암호 보호는 두 전역 및 사용자 지정 차단 암호 목록을 Azure AD에 저장 된 사용 합니다. 동일한 검사 온-프레미스 클라우드 기반 변경에 대 한 Azure AD로 수행합니다.

## <a name="design-principles"></a>디자인 원칙

Azure AD 암호 보호는 사항에 따라 이러한 원칙을 사용 하 여 설계 되었습니다.

* 도메인 컨트롤러 인터넷과 직접 통신할 필요가 없습니다.
* 새 네트워크 포트가 도메인 컨트롤러에서 열려 있지 않습니다.
* Active Directory 스키마 변경이 필요하지 않습니다. 기존 Active Directory를 사용 하는 소프트웨어 **컨테이너** 하 고 **serviceConnectionPoint** 스키마 개체입니다.
* 없는 최소 Active Directory 도메인 또는 포리스트 기능 수준을 (DFL/FFL)가 필요 합니다.
* 소프트웨어를 만들거나 보호 되는 Active Directory 도메인의 계정이 필요 하지 않습니다.
* 암호 유효성 검사 작업 중 또는 언제 든 지 사용자 일반 텍스트 암호는 도메인 컨트롤러를 종료 하지.
* 증분 배포 지원 됩니다. 하지만 암호 정책을 도메인 컨트롤러 (DC Agent) 에이전트를 설치한 에서만 적용 됩니다.
* 유니버설 암호 보호 보안 적용 되도록 모든 도메인 컨트롤러에서 DC 에이전트를 설치 하는 것이 좋습니다.

## <a name="architectural-diagram"></a>아키텍처 다이어그램

온-프레미스 Active Directory 환경에서 Azure AD 암호 보호를 배포 하기 전에 기본 디자인 및 함수 개념을 이해 하는 것이 반드시 합니다. 다음 다이어그램은 암호 보호의 구성 요소가 함께 작동 하는 방법을 보여줍니다.

![Azure AD 암호 보호 구성 요소가 함께 작동하는 방법](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD 암호 보호 프록시 서비스는 현재 Active Directory 포리스트의 도메인에 조인된 모든 머신에서 실행됩니다. 주 목적은 Azure AD에 도메인 컨트롤러에서 암호 정책 다운로드 요청을 전달할 것입니다. Azure AD에서 응답을 도메인 컨트롤러에 반환 됩니다.
* 암호 필터 DLL DC 에이전트의 운영 체제에서 사용자 암호 유효성 검사 요청을 받습니다. 이 도메인 컨트롤러에서 로컬로 실행 중인 DC 에이전트 서비스를 전달 합니다.
* 암호 보호 DC 에이전트 서비스는 암호 필터 DLL의 DC 에이전트에서에서 암호 유효성 검사 요청을 받습니다. 현재 (로컬로 사용 가능) 암호 정책을 사용 하 여 처리 하 고 결과 반환 합니다. *전달* 하거나 *실패*합니다.

## <a name="how-password-protection-works"></a>암호 보호 기능의 작동 방식

각 Azure AD 암호 보호 프록시 서비스 인스턴스 보급 자체 포리스트의 도메인 컨트롤러에 만들어를 **serviceConnectionPoint** Active Directory의 개체입니다.

암호 보호에 대 한 각 DC 에이전트 서비스가 만들어지기를 **serviceConnectionPoint** Active Directory의 개체입니다. 이 개체는 보고 및 진단에 주로 사용 됩니다.

DC 에이전트 서비스는 Azure AD에서 새 암호 정책 다운로드를 시작 하는 일을 담당 합니다. 첫 번째 단계는 프록시에 대 한 포리스트를 쿼리하여 Azure AD 암호 보호 프록시 서비스를 찾습니다 **serviceConnectionPoint** 개체입니다. 사용 가능한 프록시 서비스를 발견 되 면 DC 에이전트 암호 정책 다운로드 요청을 프록시 서비스에 보냅니다. 그러면 프록시 서비스를 Azure AD로 요청을 보냅니다. 프록시 서비스의 DC 에이전트 서비스에 대 한 응답을 반환합니다.

서비스 루트 도메인의 전용된 폴더에 정책을 저장 DC 에이전트 서비스를 Azure AD에서 새 암호 정책을 받으면 *sysvol* 폴더 공유 합니다. 또한 DC 에이전트 서비스를 도메인에 다른 DC 에이전트 서비스의 최신 정책을 복제 하는 경우이 폴더를 모니터링 합니다.

DC 에이전트 서비스는 항상 서비스 시작 시 새 정책을 요청합니다. DC 에이전트 서비스가 시작 된 후 현재 로컬로 사용할 수 있는 정책의 기간 1 시간 마다 확인 합니다. 1 시간 보다 오래 된 정책, 앞에서 설명한 대로 Azure AD에서 새 정책을 요청 DC 에이전트. 현재 정책이 1 시간 보다 오래 없으면 DC 에이전트에서 해당 정책을 사용 하 여 계속 합니다.

Azure AD 암호 보호 암호 정책 다운로드 될 때마다 해당 정책은 테 넌 트 특정입니다. 즉, 암호 정책은 항상 Microsoft 글로벌 차단 암호 목록을 목록과 테 넌 트 당 사용자 지정 차단 암호의 조합입니다.

DC 에이전트는 TCP를 통한 RPC 통해 프록시 서비스와 통신 합니다. 동적 또는 정적 RPC 포트 구성에 따라에서 이러한 호출에 대 한 프록시 서비스를 수신 대기합니다.

DC 에이전트는 되지 네트워크에서 사용 가능한 포트에서 수신 대기합니다.

프록시 서비스에는 DC 에이전트 서비스를 호출 하지 않습니다.

프록시 서비스는 상태 비저장입니다. 정책 캐시 하지 또는 다른 상태는 Azure에서 다운로드 합니다.

DC 에이전트 서비스를 사용자의 암호를 평가 하려면 최신 로컬로 사용 가능 암호 정책을 항상 사용 합니다. 암호 정책이 없고 로컬 DC에서 사용 가능한 경우 암호를 자동으로 적용 됩니다. 이 경우, 이벤트 메시지는 경고 관리자에 기록 됩니다.

Azure AD 암호 보호 실시간 정책 응용 프로그램 엔진을 하지 않습니다. Azure AD에서 암호 정책 구성 변경을 수행 때 및에 도달 하면 변경 및 모든 도메인 컨트롤러에 적용 되는 사이 지연이 있을 수 있습니다.

## <a name="foresttenant-binding-for-password-protection"></a>포리스트/테 넌 트 바인딩 암호 보호

Active Directory 포리스트에서 Azure AD 암호 보호의 배포에는 해당 포리스트의 Azure AD 사용 하 여 등록을 해야합니다. 또한 Azure AD를 사용 하 여 배포 된 각 프록시 서비스를 등록 합니다. 이 포리스트 및 프록시 등록 특정 연관 된 Azure AD 테 넌 트를 등록 하는 동안 사용 되는 자격 증명에 의해 암시적으로 식별 되는 합니다.

Active Directory 포리스트 및 포리스트 내의 모든 배포 된 프록시 서비스는 동일한 테 넌 트를 사용 하 여 등록 되어야 합니다. 다른 Azure AD에 등록 되 고 포리스트 테 넌 트에 Active Directory 포리스트 또는 모든 프록시 서비스를 보유 하는 지원 되지 않습니다. 잘못 구성 된 배포의 증상에 암호 정책을 다운로드할 수 있습니다.

## <a name="license-requirements"></a>라이선스 요구 사항

Azure AD의 모든 사용자에 게 전역 금지 된 암호 목록 혜택 적용 됩니다.

사용자 지정 암호 차단 목록에는 Azure AD Basic 라이선스가 필요합니다.

Windows Server Active Directory용 Azure AD 암호 보호에는 Azure AD Premium 라이선스가 필요합니다.

추가 라이선스 정보를 참조 하세요 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)합니다.

## <a name="download"></a>다운로드

Azure AD 암호 보호를 위한 두 명의 필수 에이전트 설치 관리자에서 사용할 수는 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=57071)합니다.

## <a name="next-steps"></a>다음 단계
[Azure AD 암호 보호 배포](howto-password-ban-bad-on-premises-deploy.md)
