---
title: 가입 장치를 Azure AD 란?
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
ms.openlocfilehash: 4af3aea7218ea8792bb66188e8df7baf9f460b0b
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462812"
---
# <a name="azure-ad-joined-devices"></a>Azure AD 조인 디바이스

Azure AD 조인은 클라우드 중심 또는 클라우드 전용 하려는 조직을 위한 것입니다. 조직 크기 또는 업계에 관계 없이 Azure AD 가입 장치를 배포할 수 있습니다. Azure AD 조인은 클라우드 및 온-프레미스 앱 및 리소스에 대 한 액세스를 사용 하도록 설정 하는 하이브리드 환경 에서도 작동 합니다.

|   | Azure AD 조인 |
| --- | --- |
| **정의** | 장치에 로그인 하려면 Azure AD 조직 계정 요구에 가입 |
| **기본 대상** | 클라우드 전용 둘 다에 대해 적합 한 및 하이브리드 조직입니다. |
|   | 조직의 모든 사용자에 게 적용 |
| **장치 소유권** | 조직 |
| **운영 체제** | 모든 Windows 10 장치 |
| **프로비전** | 셀프 서비스: Windows OOBE 또는 설정 |
|   | 대량 등록 |
|   | Windows Autopilot |
| **장치 로그인 옵션** | 조직 계정을 사용 하 여: |
|   | 암호 |
|   | 비즈니스용 Windows Hello |
|   | FIDO2.0 보안 키 (미리 보기) |
| **디바이스 관리** | 모바일 장치 관리 (예: Microsoft Intune) |
|   | Microsoft Intune 및 System Center Configuration Manager를 사용 하 여 공동 관리 |
| **주요 기능** | 클라우드 및 온-프레미스 리소스에 대 한 SSO |
|   | MDM 등록 및 MDM 규정 준수 평가 통해 조건부 액세스 |
|   | 잠금 화면에서 셀프 서비스 암호 재설정 및 Windows Hello PIN 재설정 |
|   | 장치에서 엔터프라이즈 상태 로밍 |

Azure AD 가입 장치에 로그온 해 있는 조직를 사용 하 여 Azure AD 계정. 조직의 리소스에에서 대 한 추가 될 수 있습니다 해당 Azure AD 계정을 기반으로 제한 하 고 [조건부 액세스 정책을](../conditional-access/overview.md) 장치 id에 적용 합니다.

관리자를 보호할 수 있습니다 하 고 Azure AD 컨트롤에서 Microsoft intune 또는 System Center Configuration Manager를 사용 하 여 공동 관리 시나리오에서 모바일 장치 관리 (MDM) 도구를 사용 하 여 장치를 조인 하는 추가 합니다. 이러한 도구는 저장소 암호화, 암호 복잡도, 소프트웨어 설치 및 소프트웨어 업데이트를 요구와 같은 조직에 필요한 구성을 적용 하는 수단을 제공 합니다. 관리자를 사용 하 여 Azure AD 가입 장치를 사용할 수 있는 조직의 응용 프로그램을 수행할 수 있습니다 [System Center Configuration Manager와 비즈니스용 Microsoft Store](https://docs.microsoft.com/sccm/apps/deploy-use/manage-apps-from-the-windows-store-for-business)합니다.

셀프 서비스 옵션 같은 상자 환경을 OOBE (Out of)에 대량 등록을 사용 하 여 azure AD 가입을 수행할 수 있습니다 또는 [Windows Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot)합니다.

Azure AD 가입 장치를 조직의 네트워크에 있을 때 온-프레미스 리소스에 single sign-on 액세스를 유지할 수 있습니다. 파일, 인쇄 및 다른 응용 프로그램 같은 온-프레미스 서버에 있는 Azure AD 가입 장치를 인증할 수 있습니다.

## <a name="scenarios"></a>시나리오

Azure AD 가입은 기본적으로 온-프레미스 Windows Server Active Directory 인프라가 없는 조직을 위해 고안되었으나, 다음과 같은 시나리오에서 확실히 사용할 수 있습니다.

- Azure AD 및 Intune 같은 MDM을 사용하여 클라우드 기반 인프라로 전환하려 합니다.
- 온-프레미스 도메인 가입을 사용할 수 없습니다. 예를 들어 제어되는 태블릿 및 휴대폰과 같은 모바일 디바이스가 필요한 경우입니다.
- 사용자가 기본적으로 Office 365 또는 Azure AD와 통합된 다른 SaaS 앱에 액세스해야 합니다.
- Active Directory 대신 Azure AD에서 사용자 그룹을 관리하고자 합니다. 이 시나리오에 적용할 수, 예를 들어, 비 정규직 근로자, 계약자 또는 학생과.
- 제한된 온-프레미스 인프라를 사용하는 원격 지사에서 작업자에게 조인 기능을 제공하려고 합니다.

Windows 10 디바이스에 대한 Azure AD 가입 디바이스를 구성할 수 있습니다.

Azure AD 가입 디바이스의 목표는 단순화하는 것입니다.

- 회사 소유 디바이스의 Windows 배포
- 모든 Windows 디바이스에서 조직의 앱 및 리소스에 액세스
- 회사 소유 디바이스의 클라우드 기반 관리
- 사용자가 해당 Azure AD 사용 하 여 해당 장치에 로그인 하도록 동기화 된 Active Directory 회사 또는 학교 계정 또는 합니다.

![Azure AD 조인 디바이스](./media/concept-azure-ad-join/azure-ad-joined-device.png)

다음 방법 중 하나를 사용하여 Azure AD Join을 배포할 수 있습니다.

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
- [대량 배포](https://docs.microsoft.com/intune/windows-bulk-enroll)
- [셀프 서비스 환경](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>다음 단계

- [Azure AD 조인 구현 계획](azureadjoin-plan.md)
- [장치를 가입 하는 Azure AD에서 로컬 관리자 그룹을 관리 하는 방법](assign-local-admin.md)
- [Azure portal을 사용 하 여 장치 id 관리](device-management-azure-portal.md)
- [Azure AD에서 오래 된 장치를 관리 합니다.](manage-stale-devices.md)
