---
title: 장치를 등록할 Azure AD 란?
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
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462760"
---
# <a name="azure-ad-registered-devices"></a>Azure AD 등록 디바이스

Azure AD 등록 장치의 목표 Bring Your 고유의 장치 (BYOD) 또는 모바일 장치 시나리오에 대 한 지원을 사용 하 여 사용자에 게 제공 하는 것입니다. 이러한 시나리오에서 사용자는 개인 장치를 사용 하 여 조직의 Azure Active Directory 제어 리소스를 액세스할 수 있습니다.

|   | Azure AD 등록 |
| --- | --- |
| **정의** | 장치에 로그인 하려면 조직 계정을 요구 하지 않고 Azure AD에 등록 |
| **기본 대상** | 다음 조건 사용 하 여 모든 사용자에 게 적용 합니다. |
|   | 고유한 장치 (BYOD)를 제공 합니다. |
|   | 모바일 장치 |
| **장치 소유권** | 사용자 또는 조직 |
| **운영 체제** | Windows 10, iOS, Android 및 MacOS |
| **프로비전** | Windows 10-설정 |
|   | iOS/Android-회사 포털 또는 Microsoft Authenticator 앱 |
|   | MacOS-회사 포털 |
| **장치 로그인 옵션** | 최종 사용자 로컬 자격 증명 |
|   | 암호 |
|   | Windows Hello |
|   | PIN |
|   | 생체 인식 또는 다른 장치에 대 한 패턴 |
| **디바이스 관리** | 모바일 장치 관리 (예: Microsoft Intune) |
|   | 모바일 애플리케이션 관리 |
| **주요 기능** | 클라우드 리소스에 대 한 SSO |
|   | Intune에 등록 하는 경우 조건부 액세스 |
|   | 앱 보호 정책을 통해 조건부 액세스 |
|   | Microsoft Authenticator 앱에서 휴대폰 로그인을 사용 하도록 설정 |

![Azure AD 등록 디바이스](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD 등록 장치에 Windows 10 장치에서 Microsoft 계정과 같은 로컬 계정을 사용 하 여 로그인 하지만 조직 리소스에 대 한 액세스에 대 한 연결 된 Azure AD 계정이 또한 합니다. 조직의 리소스에 대 한 액세스는 해당 Azure AD 계정을 기반으로 하는 추가 제한 및 조건부 액세스 정책은 장치 id를 적용할 수 있습니다.

관리자는 보호 하 고 Microsoft Intune과 같은 모바일 장치 관리 (MDM) 도구를 사용 하 여 이러한 Azure AD 등록 장치를 보다 세부적으로 제어할 수 있습니다. MDM에는 저장소 암호화, 암호 복잡성 및 최신 상태로 유지 하는 보안 소프트웨어 요구와 같은 조직에 필요한 구성을 적용 하는 방법을 제공 합니다. 

처음으로 또는 Windows 10 설정 메뉴를 사용 하 여 수동으로 작업 응용 프로그램에 액세스할 때 azure AD 등록을 수행할 수 있습니다. 

## <a name="scenarios"></a>시나리오

시간 및 해당 도구 이며 가정용 PC에서 복리 후생 reporting 전자 메일에 대 한 도구에 액세스 하려면 조직에서 사용자가. 조직에 Intune 호환 장치에서 액세스를 요구 하는 조건부 액세스 정책 뒤에 이러한 도구가 있습니다. 사용자가 조직 계정을 추가 하 고 Azure AD를 사용 하 여 해당 도구 이며 가정용 PC를 등록 하 고 해당 리소스에 대 한 사용자 액세스를 제공 해야 Intune 정책이 적용 됩니다.

다른 사용자는 루 팅 되었는지는 개인 Android 휴대폰에서 조직의 메일에 액세스 하려고 합니다. 회사가 준수 장치 필요 및 루 팅 된 장치를 차단 하는 Intune 준수 정책을 만들었습니다. 직원은이 장치에서 조직 리소스에 액세스 하지 못하도록 중지 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure portal을 사용 하 여 장치 id 관리](device-management-azure-portal.md)
- [Azure AD에서 오래 된 장치를 관리 합니다.](manage-stale-devices.md)
