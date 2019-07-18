---
title: 장치를 조인 된 하이브리드 Azure AD 란?
description: 디바이스 ID 관리가 사용자 환경의 리소스에 액세스하는 디바이스를 관리하는 데 어떻게 도움이 되는지 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c57180ba10322cb790c05b3f8f48043ca08b545
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462747"
---
# <a name="hybrid-azure-ad-joined-devices"></a>하이브리드 Azure AD 가입 디바이스

10년 이상, 많은 조직은 다음을 가능케 하기 위해 온-프레미스 Active Directory에 도메인 가입을 사용했습니다.

- IT 부서가 중앙 위치에서 회사 소유 디바이스를 관리하도록
- 사용자가 Active Directory 회사 또는 학교 계정으로 자신의 디바이스에 로그인하도록

온-프레미스 공간이 있는 조직에서는 일반적으로 이미징 메서드에 의존하여 디바이스를 프로비전하며, 이를 관리하는 데 **SCCM(System Center Configuration Manager)** 또는 **GP(그룹 정책)** 를 사용하기도 합니다.

사용자 환경에 온-프레미스 AD 공간이 있고 Azure Active Directory에서 제공하는 기능의 혜택을 활용하려는 경우 하이브리드 Azure AD 가입 디바이스를 구현할 수 있습니다. 이러한 장치는 온-프레미스 Active Directory에 조인 되어 Azure Active Directory를 사용 하 여 등록 된 장치입니다.

|   | 하이브리드 Azure AD 조인 |
| --- | --- |
| **정의** | 온-프레미스 AD 및 Azure AD 조직 계정에 필요한 장치에 로그인 할 가입 |
| **기본 대상** | 하이브리드 조직에 기존 온-프레미스 AD 인프라에 대 한 적합 한 |
|   | 조직의 모든 사용자에 게 적용 |
| **장치 소유권** | 조직 |
| **운영 체제** | Windows 10, 8.1 및 7 |
|   | Windows Server 2008 R2 2012/R2, 2016 및 2019 |
| **프로비전** | Windows 10, Windows Server 2016/2019 |
|   | 도메인 가입 하 여 IT 및 Azure AD Connect 또는 ADFS 구성을 통해 자동 조인 |
|   | Windows Autopilot 및 Azure AD Connect 또는 ADFS 구성을 통해 자동 조인 하 여 도메인 가입 |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 및 Windows Server 2008 R2-MSI 필요 |
| **장치 로그인 옵션** | 조직 계정을 사용 하 여: |
|   | 암호 |
|   | Win10에 대 한 비즈니스용 Windows Hello |
| **디바이스 관리** | 그룹 정책 |
|   | System Center Configuration Manager 독립 실행형 또는 Microsoft Intune을 사용 하 여 공동 관리 |
| **주요 기능** | 클라우드 및 온-프레미스 리소스에 대 한 SSO |
|   | 조건부 도메인 가입을 통해 또는 Intune을 통해 액세스 하는 경우 공동 관리 |
|   | 잠금 화면에서 셀프 서비스 암호 재설정 및 Windows Hello PIN 재설정 |
|   | 장치에서 엔터프라이즈 상태 로밍 |

![하이브리드 Azure AD 가입 디바이스](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>시나리오

사용 하 여 Azure AD 하이브리드 가입 장치는 경우:

- Active Directory 컴퓨터 인증을 사용하는 디바이스에 Win32 앱을 배포했습니다.
- 그룹 정책을 사용 하 여 장치 구성 관리를 계속 하려고 합니다.
- 계속 기존 이미징 솔루션을 사용 하 여 배포 하 고 장치를 구성 해야 합니다.
- 하위 수준 Windows 7 및 Windows 10 외에도 8.1 장치를 지원 해야

## <a name="next-steps"></a>다음 단계

- [하이브리드 Azure AD 조인 구현 계획](hybrid-azuread-join-plan.md)
- [Azure portal을 사용 하 여 장치 id 관리](device-management-azure-portal.md)
- [Azure AD에서 오래 된 장치를 관리 합니다.](manage-stale-devices.md)
