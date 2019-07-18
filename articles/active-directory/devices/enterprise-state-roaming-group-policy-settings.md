---
title: 그룹 정책 및 MDM 설정 | Microsoft Docs
description: 회사 소유의 디바이스에 사용해야 하는 그룹 정책 및 MDM(모바일 디바이스 관리) 설정에 대한 정보를 제공합니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3f2b1afa67ec36da4d4da57b296e696fd6c6910
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481936"
---
# <a name="group-policy-and-mdm-settings"></a>그룹 정책 및 MDM 설정
이러한 정책은 사용자의 디바이스 전체에 적용되므로 회사 소유의 디바이스에만 그룹 정책 및 MDM(모바일 디바이스 관리) 설정을 사용하세요. 개인의 설정 동기화를 비활성화하는 MDM 정책을 적용하면 사용자 소유의 디바이스가 해당 디바이스 사용에 부정적인 영향을 미치게 됩니다. 뿐만 아니라 해당 디바이스의 다른 사용자도 정책의 영향을 받습니다.

개인(비관리) 디바이스에 대한 로밍을 관리하려는 기업은 그룹 정책이나 MDM 대신 Azure Portal을 사용하여 로밍을 활성화 또는 비활성화할 수 있습니다.
다음은 사용 가능한 정책을 설명하는 테이블입니다.

## <a name="mdm-settings"></a>MDM 설정
MDM 정책 설정은 Windows 10 및 Windows 10 Mobile에 모두 적용됩니다.  Windows 10 Mobile 지원은 사용자의 OneDrive 계정을 통한 Microsoft 계정 기반의 로밍에 대해서만 제공됩니다.  Microsoft Azure Active Directory 기반 동기화가 지원되는 디바이스에 대한 자세한 내용은 [디바이스 및 엔드포인트](enterprise-state-roaming-windows-settings-reference.md)를 참조하세요.

| Name | 설명 |
| --- | --- |
| Microsoft 계정 연결 허용 |사용자가 디바이스에서 Microsoft 계정을 사용하여 인증 가능 |
| 내 설정 동기화 허용 |사용자가 Windows 설정 및 앱 데이터 로밍 가능: 이 정책을 사용하지 않도록 설정하면 모바일 디바이스의 동기화 및 백업이 모두 사용되지 않도록 설정됩니다. |

## <a name="group-policy-settings"></a>그룹 정책 설정
그룹 정책 설정은 Active Directory 도메인에 가입된 Windows 10 디바이스에 적용됩니다. 이 테이블에는 동기화 설정을 관리하지만 Windows 10의 엔터프라이즈 상태 로밍을 지원하지 않는(설명에 ‘사용 안 함'으로 표시) 기존 설정도 포함되어 있습니다.

이러한 설정은 `Computer Configuration > Administrative Templates > Windows Components > Sync your settings`에 있습니다. 

| Name | 설명 |
| --- | --- |
| 계정: Microsoft 계정 차단 |이 정책 설정은 사용자가 이 컴퓨터에서 새 Microsoft 계정을 추가하지 못하게 차단합니다. |
| 동기화 안 함 |사용자의 Windows 설정 및 앱 데이터 로밍 금지 |
| 개인 설정 동기화 안 함 |테마 그룹 동기화 비활성화 |
| 브라우저 설정 동기화 안 함 |Internet Explorer 그룹 동기화 비활성화 |
| 암호 동기화 안 함 |암호 그룹 동기화 비활성화 |
| 기타 Windows 설정 동기화 안 함 |기타 Windows 설정 그룹 동기화 비활성화 |
| 바탕 화면 개인 설정 동기화 안 함 |사용 안 함. 효과 없음 |
| 데이터 통신 연결에서 동기화 안 함 |셀룰러 3G 같은 데이터 통신 연결에서 로밍 비활성화 |
| 앱 동기화 안 함 |사용 안 함. 효과 없음 |
| 앱 설정 동기화 안 함 |앱 데이터 로밍 비활성화 |
| 시작 설정 동기화 안 함 |사용 안 함. 효과 없음 |

## <a name="next-steps"></a>다음 단계

개요는 [Enterprise State Roaming 개요](enterprise-state-roaming-overview.md)를 참조하세요.


