---
title: Azure AD 암호 보호 미리 보기
description: Azure AD 암호 보호 미리 보기를 사용하여 온-프레미스 Active Directory에서 약한 암호 금지하기
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 45c77aa63159a8e62f65c0e250c0ace95b0d344e
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429908"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>미리 보기: Windows Server Active Directory에 Azure AD 암호 보호 강제 적용

|     |
| --- |
| Azure AD 암호 보호 및 사용자 지정 금지 암호 목록은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

Azure AD 암호 보호는 조직의 암호 정책을 강화하기 위해 Azure AD(Azure Active Directory)에 제공되는 공개 미리 보기의 새로운 기능입니다. Azure AD 암호 보호의 온-프레미스 배포에는 Azure AD에 저장된 전역 및 사용자 지정 금지 암호 목록이 모두 사용되고 Azure AD 클라우드 기반 변경 내용과 동일한 검사를 온-프레미스에서 수행합니다.

Azure AD 암호 보호를 구성하는 소프트웨어 구성 요소는 세 가지입니다.

* Azure AD 암호 보호 프록시 서비스는 현재 Active Directory 포리스트의 도메인에 가입된 모든 컴퓨터에서 실행됩니다. 도메인 컨트롤러의 요청을 Azure AD로 전달하고 Azure AD의 응답을 도메인 컨트롤러로 반환합니다.
* Azure AD 암호 보호 DC 에이전트 서비스는 DC 에이전트 암호 필터 dll에서 암호 유효성 검사 요청을 받아서, 이 요청을 로컬에서 현재 사용 가능한 암호 정책을 사용하여 처리하고, 결과(pass\fail)를 반환합니다. 이 서비스는 새로운 버전의 암호 정책을 검색하기 위해 Azure AD 암호 보호 프록시 서비스를 주기적으로(한 시간에 한 번) 호출하는 작업을 담당합니다. Azure AD 암호 보호 프록시 서비스와 주고 받는 호출을 위한 통신은 TCP를 통한 RPC(원격 프로시저 호출)를 통해 처리됩니다. 검색이 되면 새 정책은 sysvol 폴더에 저장되며 이 곳에서 다른 도메인 컨트롤러에 복제할 수 있습니다. DC 에이전트 서비스는 다른 도메인 컨트롤러가 새 암호 정책을 기록하는 경우에 대비하여 sysvol 폴더의 변경 사항을 모니터링하며, 적절한 최근 정책이 이미 있는 경우에는 Azure AD 암호 보호 프록시 서비스의 검사를 건너뜁니다.
* DC 에이전트 암호 필터 dll은 운영 체제에서 암호 유효성 검사 요청을 수신하고 이를 도메인 컨트롤러에서 로컬로 실행되는 Azure AD 암호 보호 DC 에이전트 서비스로 전달합니다.

![Azure AD 암호 보호 구성 요소가 함께 작동하는 방법](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="license-requirements"></a>라이선스 요구 사항

전역 금지 암호 목록의 이점은 Azure AD(Azure Active Directory)의 모든 사용자에게 적용됩니다.

사용자 지정 금지 암호 목록에는 Azure AD Basic 라이선스가 필요합니다.

Windows Server Active Directory용 Azure AD 암호 보호에는 Azure AD Premium 라이선스가 필요합니다.

비용을 비롯한 추가 라이선스 정보는 [Azure Active Directory 가격 책정 사이트](https://azure.microsoft.com/pricing/details/active-directory/)에서 확인할 수 있습니다.

## <a name="download"></a>다운로드

Azure AD 암호 보호에 필요한 두 가지 설치 관리자가 있으며, [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=57071)에서 다운로드할 수 있습니다.

## <a name="answers-to-common-questions"></a>일반적인 질문에 대한 답변

* 도메인 컨트롤러에서 인터넷에 연결할 필요가 없습니다. 인터넷 연결이 필요한 유일한 컴퓨터는 Azure AD 암호 보호 프록시 서비스를 실행하는 컴퓨터입니다.
* 도메인 컨트롤러에는 네트워크 포트가 열려 있지 않습니다.
* Active Directory 스키마 변경이 필요하지 않습니다.
* 기존 Active Directory 컨테이너 및 serviceConnectionPoint 스키마 개체가 소프트웨어에 사용됩니다.
* 최소 Active Directory 도메인 또는 포리스트 기능 수준(DFL\FFL) 요구 사항은 없습니다.
* 이 소프트웨어는 보호하는 Active Directory 도메인의 계정을 만들거나 요구하지 않습니다.
* 증분 배포는 암호 정책이 도메인 컨트롤러 에이전트가 설치된 위치에만 적용된다는 절충안과 함께 지원됩니다.
* 암호 보호 기능이 적용되도록 모든 DC에서 DC 에이전트를 설치하는 것이 좋습니다. 
* Azure AD 암호 보호는 실시간 정책 애플리케이션 엔진이 아닙니다. 암호 정책 구성이 변경되는 시간과 이것이 모든 도메인 컨트롤러에 도달하여 강제 적용되는 시간 사이에는 지연이 있을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 암호 보호 배포](howto-password-ban-bad-on-premises-deploy.md)
