---
title: MFA 서버 구성-Azure Active Directory
description: Azure Portal에서 Azure MFA 서버에 대 한 설정을 구성 하는 방법을 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70777c80cdee99f13050d7cd342f22d59da13d73
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964113"
---
# <a name="configure-mfa-server-settings"></a>MFA 서버 설정 구성

이 문서에서는 Azure Portal에서 Azure MFA 서버 설정을 관리 하는 방법을 설명 합니다.

> [!IMPORTANT]
> Microsoft는 2019년 7월 1일부터 더 이상 새 배포를 위한 MFA 서버를 제공하지 않습니다. 신규 사용자의 다단계 인증이 필요한 고객은 클라우드 기반 Azure Multi-Factor Authentication을 사용해야 합니다. 7월 1일 이전에 MFA 서버를 활성화한 기존 고객은 종전과 같이 최신 버전 및 이후 업데이트를 다운로드하고 활성화 자격 증명을 생성할 수 있습니다.

다음 MFA 서버 설정을 사용할 수 있습니다.

| 기능 | Description |
| ------- | ----------- |
| 서버 설정 | MFA 서버를 다운로드하고 활성화 자격 증명을 생성하여 환경을 초기화합니다. |
| [일회성 바이패스](#one-time-bypass) | 사용자가 제한 된 시간 동안 multi-factor authentication을 수행 하지 않고 인증 하도록 허용 합니다. |
| [캐싱 규칙](#caching-rules) |  캐싱은 첫 번째 요청이 진행 중인 동안 VPN과 같은 온-프레미스 시스템이 여러 확인 요청을 전송하는 경우 주로 사용됩니다. 이 기능을 사용하면 사용자가 진행 중인 첫 번째 확인에 성공한 후 자동으로 후속 요청이 성공할 수 있습니다. |
| 서버 상태 | 버전, 상태, IP 및 마지막 통신 시간과 날짜를 비롯한 온-프레미스 MFA 서버의 상태를 봅니다. |

## <a name="one-time-bypass"></a>일회성 바이패스

일회성 바이패스 기능을 사용 하면 사용자가 multi-factor authentication을 수행 하지 않고 단일 시간을 인증할 수 있습니다. 바이패스는 임시적이며 지정된 시간(초) 이후 만료됩니다. 모바일 앱 또는 전화가 알림 또는 전화 통화를 받지 못하는 경우 일회성 바이패스를 사용하여 사용자가 원하는 리소스에 액세스하도록 할 수 있습니다.

일회성 바이패스를 만들려면 다음 단계를 완료 합니다.

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory**를 검색 하 고 선택한 다음 **보안**  >  **MFA**  >  **일회성 바이패스**로 이동 합니다.
1. **추가**를 선택합니다.
1. 필요한 경우 바이패스에 대한 복제 그룹을 선택합니다.
1. 사용자 이름을 `username\@domain.com`으로 입력합니다. 바이패스를 마지막으로 해야 하는 시간 (초)과 바이패스의 이유를 입력 합니다.
1. **추가**를 선택합니다. 시간 제한은 즉시 적용됩니다. 일회성 바이패스가 만료되기 전에 사용자가 로그인해야 합니다.

동일한 창에서 일회성 바이패스 보고서를 볼 수도 있습니다.

## <a name="caching-rules"></a>캐싱 규칙

_캐싱_ 기능을 사용하여 사용자가 인증된 후 인증 시도를 할 수 있는 시간 기간을 설정할 수 있습니다. 지정된 시간 기간 이내에 사용자에 대한 이후 인증 시도는 자동으로 성공합니다.

캐싱은 첫 번째 요청이 진행 중인 동안 VPN과 같은 온-프레미스 시스템이 여러 확인 요청을 전송하는 경우 주로 사용됩니다. 이 기능을 사용하면 사용자가 진행 중인 첫 번째 확인에 성공한 후 자동으로 후속 요청이 성공할 수 있습니다.

>[!NOTE]
> 캐싱 기능은 Azure AD(Azure Active Directory)에 로그인하는 데 사용하기 위한 것이 아닙니다.

캐싱을 설정 하려면 다음 단계를 완료 합니다.

1. **Azure Active Directory** > **보안** > **MFA** > **캐싱 규칙**으로 이동합니다.
1. **추가**를 선택합니다.
1. 드롭다운 목록에서 **캐시 형식**을 선택합니다. 최대 **캐시 초 수**를 입력합니다.
1. 필요한 경우 인증 유형을 선택하고 애플리케이션을 지정합니다.
1. **추가**를 선택합니다.

## <a name="next-steps"></a>다음 단계

MFA 서버 자체의 웹 콘솔에서 추가 MFA 서버 구성 옵션을 사용할 수 있습니다. [고가용성을 위해 AZURE MFA 서버를 구성할](howto-mfaserver-deploy-ha.md)수도 있습니다.
