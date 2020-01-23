---
title: 하이브리드 Azure AD 조인 장치 란?
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
ms.openlocfilehash: 15cdaba7d63d72aab25757e7ba6f5eadc48e026a
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512252"
---
# <a name="hybrid-azure-ad-joined-devices"></a>하이브리드 Azure AD 가입 디바이스

10년 이상, 많은 조직은 다음을 가능케 하기 위해 온-프레미스 Active Directory에 도메인 가입을 사용했습니다.

- IT 부서가 중앙 위치에서 회사 소유 디바이스를 관리하도록
- 사용자가 Active Directory 회사 또는 학교 계정으로 자신의 디바이스에 로그인하도록

일반적으로 온-프레미스의 공간이 있는 조직은 장치를 프로 비전 하는 이미징 방법을 사용 하며, 종종 **Configuration Manager** 또는 **그룹 정책 (GP)** 을 사용 하 여 관리 합니다.

사용자 환경에 온-프레미스 AD 공간이 있고 Azure Active Directory에서 제공하는 기능의 혜택을 활용하려는 경우 하이브리드 Azure AD 가입 디바이스를 구현할 수 있습니다. 이러한 장치는 온-프레미스 Active Directory에 조인 되 고 Azure Active Directory에 등록 된 장치입니다.

|   | 하이브리드 Azure AD 조인 |
| --- | --- |
| **정의** | 온-프레미스 AD 및 Azure AD에 조인 하 여 조직 계정이 장치에 로그인 하도록 요구 |
| **기본 대상** | 기존 온-프레미스 AD 인프라를 사용 하는 하이브리드 조직에 적합 |
|   | 조직의 모든 사용자에 게 적용 가능 |
| **장치 소유권** | 조직 |
| **운영 체제** | Windows 10, 8.1 및 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 및 2019 |
| **프로비전** | Windows 10, Windows Server 2016/2019 |
|   | Azure AD Connect 또는 ADFS 구성을 통한 IT 및 autojoin 도메인 가입 |
|   | Azure AD Connect 또는 ADFS 구성을 통한 Windows Autopilot 및 autojoin의 도메인 가입 |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 및 Windows Server 2008 R2-MSI 필요 |
| **장치 로그인 옵션** | 다음을 사용 하는 조직 계정: |
|   | 암호 |
|   | 비즈니스용 Windows Hello Win10 |
| **디바이스 관리** | 그룹 정책 |
|   | Microsoft Intune를 사용 하 여 독립 실행형 또는 공동 관리 Configuration Manager |
| **주요 기능** | 클라우드 및 온-프레미스 리소스 모두에 SSO |
|   | 공동 관리 되는 경우 도메인 가입 또는 Intune을 통한 조건부 액세스 |
|   | 잠금 화면에서 셀프 서비스 암호 재설정 및 Windows Hello PIN 다시 설정 |
|   | 장치에서 Enterprise State Roaming |

![하이브리드 Azure AD 가입 디바이스](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>시나리오

다음과 같은 경우 Azure AD 하이브리드 가입 장치를 사용 합니다.

- Active Directory 컴퓨터 인증을 사용하는 디바이스에 Win32 앱을 배포했습니다.
- 계속 해 서 그룹 정책를 사용 하 여 장치 구성을 관리 하려고 합니다.
- 기존 이미징 솔루션을 계속 사용 하 여 장치를 배포 및 구성 하려고 합니다.
- Windows 10 외에도 하위 수준 Windows 7 및 8.1 장치를 지원 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [하이브리드 Azure AD 조인 구현 계획](hybrid-azuread-join-plan.md)
- [Azure Portal를 사용 하 여 장치 id 관리](device-management-azure-portal.md)
- [Azure AD에서 오래 된 장치 관리](manage-stale-devices.md)
