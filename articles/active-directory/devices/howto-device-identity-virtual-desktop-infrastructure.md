---
title: 장치 id 및 데스크톱 가상화-Azure Active Directory
description: VDI와 Azure AD 장치 id를 함께 사용 하는 방법을 알아봅니다.
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
ms.openlocfilehash: 1a1cba2c4572b2f898f631aefbbf316fae1195ac
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596351"
---
# <a name="device-identity-and-desktop-virtualization"></a>장치 id 및 데스크톱 가상화

관리자는 일반적으로 조직에서 Windows 운영 체제를 호스팅하는 VDI (가상 데스크톱 인프라) 플랫폼을 배포 합니다. 관리자는 VDI를 다음에 배포 합니다.

- 관리를 간소화 합니다.
- 리소스를 통합 하 고 중앙 집중화 하 여 비용을 절감 합니다.
- 언제 어디서 나 모든 장치에서 가상 데스크톱에 액세스할 수 있는 최종 사용자 이동성 및 자유를 제공 합니다.

가상 데스크톱에는 다음과 같은 두 가지 기본 유형이 있습니다.

- 영구적
- 비영구적

영구 버전은 각 사용자 또는 사용자 풀에 고유한 데스크톱 이미지를 사용 합니다. 이러한 고유한 데스크톱은 나중에 사용할 수 있도록 사용자 지정 하 고 저장할 수 있습니다. 

비영구 버전은 사용자가 필요에 따라에 액세스할 수 있는 데스크톱 컬렉션을 사용 합니다. 이러한 비 영구적 데스크톱은 사용자가 로그 아웃 한 후 원래 상태로 되돌아갑니다.

이 문서에서는 관리자에 게 장치 id 및 VDI에 대 한 지원을 제공 하는 Microsoft의 지침을 설명 합니다. 장치 id에 대 한 자세한 내용은 [장치 Id 정의](overview.md)문서를 참조 하세요.

## <a name="supported-scenarios"></a>지원되는 시나리오

VDI 환경에 대해 Azure AD에서 장치 id를 구성 하기 전에 지원 되는 시나리오를 숙지 합니다. 다음 표에서는 지원 되는 프로 비전 시나리오를 보여 줍니다. 이 컨텍스트에서 프로 비전 하는 것은 관리자가 최종 사용자 개입 없이도 장치 id를 대규모로 구성할 수 있음을 의미 합니다.

| 장치 id 유형 | Id 인프라 | Windows 디바이스 | VDI 플랫폼 버전 | 지원됨 |
| --- | --- | --- | --- | --- |
| 하이브리드 Azure AD 가입 | 제휴 | Windows 현재 * * * 및 Windows 하위 수준 * * * * | 영구적 | 예 |
|   |   |   | 비영구 | 예 |
|   | 관리 됨 * * | Windows 현재 및 Windows 하위 수준 | 영구적 | 예 |
|   |   | Windows 하위 수준 | 비영구 | 예 |
|   |   | Windows 현재 | 비영구 | 아니오 |
| Azure AD 가입 | 페더레이션 | Windows 현재 | 영구적 | 아니오 |
|   |   |   | 비영구 | 아니오 |
|   | 관리 | Windows 현재 | 영구적 | 아니오 |
|   |   |   | 비영구 | 아니오 |
| Azure AD 등록 | 페더레이션 | Windows 현재 | 영구적 | 아니오 |
|   |   |   | 비영구 | 아니오 |
|   | 관리 | Windows 현재 | 영구적 | 아니오 |
|   |   |   | 비영구 | 아니오 |

\* **페더레이션된** id 인프라 환경은 AD FS 또는 기타 타사 IDP 같은 id 공급자를 사용 하는 환경을 나타냅니다.

**관리 되** 는 id 인프라 환경 \* \*는 [원활한 Single Sign-On](../hybrid/how-to-connect-sso.md)를 사용 하 여 AZURE AD가 [phs (암호 해시 동기화](../hybrid/whatis-phs.md) ) 또는 [pta (통과 인증)](../hybrid/how-to-connect-pta.md) 로 배포 된 id 공급자로 Azure AD를 사용 하는 환경을 나타냅니다.

\*\*\* **windows 현재** 장치는 windows 10, windows server 2016 및 windows server 2019를 나타냅니다.

windows **하위 수준** 장치 \*\* \*\*windows 7, Windows 8.1, windows Server 2008 R2, windows server 2012 및 Windows server 2012 r 2를 나타냅니다. Windows 7에 대 한 지원 정보 [는 windows 7 지원 종료](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)를 참조 하세요. Windows Server 2008 r 2에 대 한 지원 정보는 [Windows server 2008 지원 종료](https://www.microsoft.com/cloud-platform/windows-server-2008)를 참조 하세요.

## <a name="microsofts-guidance"></a>Microsoft 지침

관리자는 하이브리드 Azure AD 조인을 구성 하는 방법을 알아보려면 id 인프라에 따라 다음 문서를 참조 해야 합니다.

- [페더레이션된 환경에 대 한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-federated-domains.md)
- [관리 환경에 대 한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-managed-domains.md)

시스템 준비 도구 (sysprep.exe)를 사용 하는 경우 및 설치를 위해 Windows 10 1809 이전 이미지를 사용 하는 경우 하이브리드 Azure AD에 가입 된 Azure AD에 이미 등록 된 장치에서 이미지를 사용 하 고 있지 않은지 확인 합니다.

VM (가상 머신) 스냅숏을 사용 하 여 추가 Vm을 만드는 경우, Azure AD에 이미 등록 된 VM의 스냅숏이 하이브리드 Azure AD 조인으로 사용 되지 않는지 확인 합니다.

비영구 VDI를 배포 하는 경우 IT 관리자는 Azure AD에서 오래 된 장치를 관리 하는 데 주의를 기울여야 합니다. IT 관리자는 아래 지침을 구현 하는 것이 좋습니다. 이렇게 하지 않으면 영구적이 지 않은 VDI 플랫폼에서 등록 된 오래 된 하이브리드 Azure AD 조인 장치가 많은 디렉터리에 생성 됩니다.

- 데스크톱을 VDI 기반으로 표시 하는 컴퓨터의 표시 이름에 접두사를 만들고 사용 합니다.
- 다음 명령을 로그 오프 스크립트의 일부로 구현 합니다. 이러한 명령은 Azure AD에 대 한 최상의 호출을 트리거하여 장치를 삭제 합니다.
   - Windows 현재 장치의 경우 – dsregcmd/leave
   - Windows 하위 수준 장치의 경우-autoworkplace/leave
- [오래 된 장치를 관리](manage-stale-devices.md)하는 프로세스를 정의 하 고 구현 합니다.
   - 비영구 하이브리드 Azure AD 조인 장치를 식별 하는 전략을 사용 하는 경우 이러한 장치를 정리 하는 방법에 대 한 정보를 활용 하 여 오래 된 많은 장치에서 디렉터리가 소비 되지 않도록 할 수 있습니다.
 
## <a name="next-steps"></a>다음 단계

[페더레이션된 환경에 대 한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-federated-domains.md)
