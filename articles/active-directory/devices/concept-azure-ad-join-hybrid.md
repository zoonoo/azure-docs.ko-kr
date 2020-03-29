---
title: 하이브리드 Azure AD 조인 장치란 무엇입니까?
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512252"
---
# <a name="hybrid-azure-ad-joined-devices"></a>하이브리드 Azure AD 가입 디바이스

10년 이상, 많은 조직은 다음을 가능케 하기 위해 온-프레미스 Active Directory에 도메인 가입을 사용했습니다.

- IT 부서가 중앙 위치에서 회사 소유 디바이스를 관리하도록
- 사용자가 Active Directory 회사 또는 학교 계정으로 자신의 디바이스에 로그인하도록

일반적으로 온-프레미스 설치 공간이 있는 조직은 이미징 메서드를 사용하여 장치를 프로비전하고 구성 **관리자** 또는 **GP(그룹 정책)를** 사용하여 이를 관리합니다.

사용자 환경에 온-프레미스 AD 공간이 있고 Azure Active Directory에서 제공하는 기능의 혜택을 활용하려는 경우 하이브리드 Azure AD 가입 디바이스를 구현할 수 있습니다. 이러한 장치는 온-프레미스 Active Directory에 가입되어 Azure Active Directory에 등록된 장치입니다.

|   | 하이브리드 Azure AD 조인 |
| --- | --- |
| **정의** | 온-프레미스 AD 및 Azure AD에 가입하여 조직 계정이 장치에 로그인하도록 요구합니다. |
| **1차 잠재고객** | 기존 온-프레미스 AD 인프라를 갖춘 하이브리드 조직에 적합 |
|   | 조직의 모든 사용자에게 적용 가능 |
| **디바이스 소유권** | 조직 |
| **운영 체제** | 윈도우 10, 8.1 및 7 |
|   | 윈도우 서버 2008/R2, 2012/R2, 2016 및 2019 |
| **프로비전** | 윈도우 10, 윈도우 서버 2016/2019 |
|   | AZURE AD Connect 또는 ADFS 구성을 통해 IT에서 도메인 가입 및 자동 조인 |
|   | Windows 자동 조종 장치에 의한 도메인 조인 및 Azure AD 연결 또는 ADFS 구성을 통한 자동 조인 |
|   | 윈도우 8.1, 윈도우 7, 윈도우 서버 2012 R2, 윈도우 서버 2012, 윈도우 서버 2008 R2 - MSI필요 |
| **장치 로그인 옵션** | 다음을 사용하는 조직 계정: |
|   | 암호 |
|   | Win10을 위한 비즈니스를 위한 Windows 안녕하세요 |
| **장치 관리** | 그룹 정책 |
|   | 구성 관리자 독립 실행형 또는 Microsoft Intune과의 공동 관리 |
| **주요 기능** | 클라우드 및 온-프레미스 리소스에 대한 SSO |
|   | 공동 관리되는 경우 도메인 조인또는 Intune을 통한 조건부 액세스 |
|   | 셀프 서비스 암호 재설정 및 잠금 화면에서 Windows Hello PIN 재설정 |
|   | 장치 간에 엔터프라이즈 상태 로밍 |

![하이브리드 Azure AD 가입 디바이스](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>시나리오

다음과 같은 경우 Azure AD 하이브리드 조인 장치를 사용합니다.

- Active Directory 컴퓨터 인증을 사용하는 디바이스에 Win32 앱을 배포했습니다.
- 그룹 정책을 계속 사용하여 장치 구성을 관리하려고 합니다.
- 기존 이미징 솔루션을 계속 사용하여 장치를 배포하고 구성하려고 합니다.
- 당신은 윈도우 에 추가 다운 레벨 윈도우 7 및 8.1 장치를 지원 해야 합니다 10

## <a name="next-steps"></a>다음 단계

- [하이브리드 Azure AD 조인 구현 계획](hybrid-azuread-join-plan.md)
- [Azure 포털을 사용하여 장치 ID 관리](device-management-azure-portal.md)
- [Azure AD에서 부실 디바이스 관리](manage-stale-devices.md)
