---
title: Azure AD 등록 장치는 무엇입니까?
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
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67462760"
---
# <a name="azure-ad-registered-devices"></a>Azure AD 등록 디바이스

Azure AD 등록 장치의 목표는 사용자에게 BYOD(사용자 소유 장치 가져오기) 또는 모바일 장치 시나리오에 대한 지원을 제공하는 것입니다. 이러한 시나리오에서 사용자는 개인 장치를 사용하여 조직의 Azure Active Directory 제어 리소스에 액세스할 수 있습니다.

|   | Azure AD 등록 |
| --- | --- |
| **정의** | 장치에 로그인할 조직 계정 필요 없이 Azure AD에 등록됨 |
| **1차 잠재고객** | 다음 기준을 가진 모든 사용자에게 적용 가능: |
|   | BYOD(Bring Your Own Device) |
|   | 모바일 디바이스 |
| **디바이스 소유권** | 사용자 또는 조직 |
| **운영 체제** | 윈도우 10, 아이폰 OS, 안드로이드, 맥 OS |
| **프로비전** | 윈도우 10 – 설정 |
|   | 아이폰 OS / 안드로이드 - 회사 포털 또는 마이크로 소프트 인증자 응용 프로그램 |
|   | 맥OS – 회사 포털 |
| **장치 로그인 옵션** | 최종 사용자 로컬 자격 증명 |
|   | 암호 |
|   | Windows Hello |
|   | PIN |
|   | 다른 장치에 대한 생체 인식 또는 패턴 |
| **장치 관리** | 모바일 장치 관리(예: Microsoft Intune) |
|   | 모바일 애플리케이션 관리 |
| **주요 기능** | SSO에서 클라우드 리소스로 |
|   | Intune에 등록할 때 조건부 액세스 |
|   | 앱 보호 정책을 통한 조건부 액세스 |
|   | Microsoft 인증앱으로 전화 로그인 가능 |

![Azure AD 등록 디바이스](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD 등록 된 장치는 Windows 10 장치에서 Microsoft 계정과 같은 로컬 계정을 사용 하 여 로그인 하지만 또한 조직 리소스에 대 한 액세스를 위해 연결 된 Azure AD 계정. 조직의 리소스에 대한 액세스는 장치 ID에 적용되는 Azure AD 계정 및 조건부 액세스 정책에 따라 더 제한될 수 있습니다.

관리자는 Microsoft Intune과 같은 MDM(모바일 장치 관리) 도구를 사용하여 이러한 Azure AD 등록 장치를 보호하고 추가로 제어할 수 있습니다. MDM은 저장소를 암호화하고 암호 복잡성을 유지하며 보안 소프트웨어를 업데이트하도록 요구하는 것과 같은 조직에 필요한 구성을 적용하는 수단을 제공합니다. 

Azure AD 등록은 처음으로 작업 응용 프로그램에 액세스하거나 Windows 10 설정 메뉴를 수동으로 사용할 때 수행할 수 있습니다. 

## <a name="scenarios"></a>시나리오

조직의 사용자는 홈 PC에서 전자 메일, 보고 시간 해제 및 혜택 등록을 위한 도구에 액세스하려고 합니다. 조직에는 Intune 호환 장치에서 액세스해야 하는 조건부 액세스 정책 뒤에 이러한 도구가 있습니다. 사용자는 조직 계정을 추가하고 Azure AD에 홈 PC를 등록하고 필요한 Intune 정책이 적용되어 사용자에게 리소스에 대한 액세스 권한을 부여합니다.

다른 사용자는 루팅 된 개인 Android 휴대 전화에서 조직 전자 메일에 액세스하려고합니다. 회사에서 규정을 준수하는 장치가 필요하며 루팅된 장치를 차단하는 Intune 준수 정책을 만들었습니다. 직원이 이 장치의 조직 리소스에 액세스하지 못하도록 중지됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure 포털을 사용하여 장치 ID 관리](device-management-azure-portal.md)
- [Azure AD에서 부실 디바이스 관리](manage-stale-devices.md)
