---
title: Azure AD 등록 장치는 무엇 인가요?
description: Azure AD 등록 장치에서 사용자에 게 사용자 장치 (BYOD) 또는 모바일 장치 시나리오를 지 원하는 기능을 제공 하는 방법을 알아봅니다.
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
ms.openlocfilehash: 72c40b7962090492f8b4b85e555a947130c3628d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91256441"
---
# <a name="azure-ad-registered-devices"></a>Azure AD 등록 디바이스

Azure AD 등록 장치의 목표는 사용자에 게 BYOD (사용자 장치) 또는 모바일 장치 시나리오에 대 한 지원을 제공 하는 것입니다. 이러한 시나리오에서 사용자는 개인 장치를 사용 하 여 조직의 Azure Active Directory 제어 되는 리소스에 액세스할 수 있습니다.

| 등록 된 Azure AD | Description |
| --- | --- |
| **정의** | 조직 계정이 장치에 로그인 할 필요 없이 Azure AD에 등록 됨 |
| **기본 대상 그룹** | 다음 조건에 해당 하는 모든 사용자에 게 적용 됩니다. |
|   | BYOD(Bring Your Own Device) |
|   | 모바일 디바이스 |
| **디바이스 소유권** | 사용자 또는 조직 |
| **운영 체제** | Windows 10, iOS, Android 및 MacOS |
| **프로비전** | Windows 10 – 설정 |
|   | iOS/Android – 회사 포털 또는 Microsoft Authenticator 앱 |
|   | MacOS – 회사 포털 |
| **디바이스 로그인 옵션** | 최종 사용자 로컬 자격 증명 |
|   | 암호 |
|   | Windows Hello |
|   | PIN |
|   | 다른 장치의 생체 인식 또는 패턴 |
| **디바이스 관리** | 모바일 디바이스 관리(예: Microsoft Intune) |
|   | 모바일 애플리케이션 관리 |
| **주요 기능** | 클라우드 리소스에 대 한 SSO |
|   | Intune에 등록 된 조건부 액세스 |
|   | 앱 보호 정책을 통한 조건부 액세스 |
|   | Microsoft Authenticator 앱으로 휴대폰 로그인을 사용 하도록 설정 합니다. |

![Azure AD 등록 디바이스](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD 등록 장치는 Windows 10 장치에서 Microsoft 계정와 같은 로컬 계정을 사용 하 여 로그인 하지만 조직 리소스에 액세스 하기 위해 연결 된 Azure AD 계정도 추가로 포함 합니다. 조직의 리소스에 대한 액세스는 해당 Azure AD 계정 및 디바이스 ID에 적용된 조건부 액세스 정책에 따라 추가로 제한할 수 있습니다.

관리자는 Microsoft Intune 같은 MDM (모바일 장치 관리) 도구를 사용 하 여 이러한 Azure AD 등록 장치를 안전 하 고 제어할 수 있습니다. MDM은 저장소를 암호화 하 고, 암호 복잡성을 유지 하 고, 보안 소프트웨어를 업데이트 해야 하는 등 조직에서 필요한 구성을 적용 하는 수단을 제공 합니다. 

처음으로 회사 응용 프로그램에 액세스 하거나 Windows 10 설정 메뉴를 사용 하 여 수동으로 Azure AD 등록을 수행할 수 있습니다. 

## <a name="scenarios"></a>시나리오

조직의 사용자는 자신의 집 PC에서 전자 메일, 보고 시간 및 혜택 등록에 대 한 도구에 액세스 하려고 합니다. 조직에는 Intune 호환 장치에서 액세스 해야 하는 조건부 액세스 정책 뒤에 이러한 도구가 있습니다. 사용자가 자신의 조직 계정을 추가 하 고 해당 가정용 PC를 Azure AD에 등록 하면 사용자에 게 리소스에 대 한 액세스 권한을 부여 하는 데 필요한 Intune 정책이 적용 됩니다.

다른 사용자가 루 팅 된 개인 Android 휴대폰에서 조직 메일에 액세스 하려고 합니다. 회사에 규정 준수 장치가 필요 하며, 모든 루 팅 된 장치를 차단 하는 Intune 준수 정책을 만들었습니다. 직원이이 장치에서 조직 리소스에 액세스 하는 것을 중지 했습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 디바이스 ID 관리](device-management-azure-portal.md)
- [Azure AD에서 부실 디바이스 관리](manage-stale-devices.md)
