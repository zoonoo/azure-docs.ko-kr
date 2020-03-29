---
title: 장치 ID 및 데스크톱 가상화 - Azure Active Directory
description: VDI 및 Azure AD 장치 ID를 함께 사용하는 방법 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b431cee3b8e5fc168dec2766442d6f6b9869d1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900366"
---
# <a name="device-identity-and-desktop-virtualization"></a>장치 ID 및 데스크톱 가상화

관리자는 일반적으로 조직에서 Windows 운영 체제를 호스팅하는 VDI(가상 데스크톱 인프라) 플랫폼을 배포합니다. 관리자는 VDI를 배포하여 다음을 수행합니다.

- 관리를 간소화합니다.
- 리소스의 통합 및 중앙 집중화를 통해 비용을 절감합니다.
- 언제 어디서나 모든 장치에서 가상 데스크톱에 액세스할 수 있는 이동성과 자유를 제공합니다.

가상 데스크톱에는 두 가지 기본 유형이 있습니다.

- 영구적
- 비영구적

영구 버전은 각 사용자 또는 사용자 풀에 대해 고유한 데스크톱 이미지를 사용합니다. 이러한 고유한 데스크톱은 나중에 사용할 수 위해 사용자 지정하고 저장할 수 있습니다. 

비영구 버전은 사용자가 필요에 따라 액세스할 수 있는 데스크톱 컬렉션을 사용합니다. 이러한 비영구 데스크톱은 사용자가 로그아웃한 후 원래 상태로 되돌아갑니다.

이 문서에서는 장치 ID 및 VDI지원에 대한 관리자에 대한 Microsoft의 지침을 다룹니다. 장치 ID에 대한 자세한 내용은 [장치 ID입니다.](overview.md)

## <a name="supported-scenarios"></a>지원되는 시나리오

VDI 환경에 대한 Azure AD에서 장치 ID를 구성하기 전에 지원되는 시나리오를 숙지하십시오. 아래 표에서는 지원되는 프로비저닝 시나리오를 보여 줍니다. 이 컨텍스트에서 프로비전은 관리자가 최종 사용자 상호 작용없이 대규모로 장치 ID를 구성할 수 있음을 의미합니다.

| 장치 ID 유형 | ID 인프라 | Windows 디바이스 | VDI 플랫폼 버전 | 지원됨 |
| --- | --- | --- | --- | --- |
| 하이브리드 Azure AD 가입 | 페더레이션* | 윈도우 현재 *** 및 윈도우 다운 레벨 **** | 영구적 | yes |
|   |   | 윈도우 전류 | 비영구 | 예 |
|   |   | Windows 하위 수준 | 비영구 | yes |
|   | 관리** | 윈도우 현재 및 윈도우 다운 레벨 | 영구적 | yes |
|   |   | 윈도우 전류 | 비영구 | 예 |
|   |   | Windows 하위 수준 | 비영구 | yes |
| Azure AD 가입 | 페더레이션 | 윈도우 전류 | 영구적 | 예 |
|   |   |   | 비영구 | 예 |
|   | 관리 | 윈도우 전류 | 영구적 | 예 |
|   |   |   | 비영구 | 예 |
| Azure AD 등록 | 페더레이션 | 윈도우 전류 | 영구적 | 예 |
|   |   |   | 비영구 | 예 |
|   | 관리 | 윈도우 전류 | 영구적 | 예 |
|   |   |   | 비영구 | 예 |

\***페더레이션된** ID 인프라 환경은 AD FS 또는 기타 타사 IDP와 같은 ID 공급자가 있는 환경을 나타냅니다.

\*\***관리되는** ID 인프라 환경은 Azure AD가 원활한 [단일 사인온을](../hybrid/how-to-connect-sso.md)통해 [PHS(암호 해시 동기화)](../hybrid/whatis-phs.md) 또는 [PTA(통과 인증)로](../hybrid/how-to-connect-pta.md) 배포된 ID 공급자로 있는 환경을 나타냅니다.

\*\*\***Windows 현재** 장치는 Windows 10, Windows 서버 2016 및 Windows 서버 2019를 나타냅니다.

\*\*\*\***Windows 하위 수준** 장치는 Windows 7, Windows 8.1, Windows 서버 2008 R2, Windows Server 2012 및 Windows 서버 2012 R2를 나타냅니다. Windows 7에 대한 지원 정보는 [Windows 7 지원이 종료되는](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)것을 참조하십시오. Windows Server 2008 R2에 대한 지원 정보는 [Windows Server 2008 지원 종료 를](https://www.microsoft.com/cloud-platform/windows-server-2008)참조하십시오.

## <a name="microsofts-guidance"></a>마이크로소프트의 지침

관리자는 ID 인프라에 따라 다음 문서를 참조하여 하이브리드 Azure AD 조인을 구성하는 방법을 알아두어야 합니다.

- [페더레이션 환경에 대한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-federated-domains.md)
- [관리되는 환경에 대한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-managed-domains.md)

시스템 준비 도구(sysprep.exe)에 의존하고 있고 설치를 위해 Windows 10 1809 이전 이미지를 사용하는 경우 이미지가 Azure AD에 이미 등록된 장치에서 하이브리드 Azure AD가 조인된 것이 아닌지 확인합니다.

추가 VM을 만들기 위해 VM(가상 컴퓨터) 스냅숏에 의존하는 경우 스냅숏이 하이브리드 Azure AD 조인으로 Azure AD에 이미 등록된 VM이 아닌지 확인합니다.

비영구 VDI를 배포할 때 IT 관리자는 Azure AD에서 오래된 장치를 관리하는 데 세심한 주의를 기울여야 합니다. IT 관리자는 아래 지침을 구현하는 것이 좋습니다. 이렇게 하지 않으면 비영구 VDI 플랫폼에서 등록된 오래된 하이브리드 Azure AD 조인 장치가 많이 있습니다.

- 데스크톱을 VDI 기반으로 나타내는 컴퓨터의 표시 이름에 접두사를 만들고 사용합니다.
- 로그오프 스크립트의 일부로 다음 명령을 구현합니다. 이 명령은 Azure AD에 대한 최상의 호출을 트리거하여 장치를 삭제합니다.
   - Windows 다운 레벨 장치의 경우 - autoworkplace.exe /leave
- 오래된 장치를 관리하기 위한 프로세스를 정의하고 [구현합니다.](manage-stale-devices.md)
   - 비지속적 하이브리드 Azure AD 조인 장치를 식별하는 전략이 있으면 디렉터리에 많은 오래된 장치가 사용되지 않도록 이러한 장치를 정리하는 데 더욱 공격적일 수 있습니다.
 
## <a name="next-steps"></a>다음 단계

[페더레이션 환경에 대한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-federated-domains.md)
