---
title: Azure Multi-Factor Authentication 데이터 보존
description: Azure Multi-Factor Authentication에서 귀하와 귀하의 사용자에 대해 저장하는 개인 및 조직 데이터와 영역의 국가/지역 내에 유지되는 데이터에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25322ad9a5d57094f44ccbad312091214ae8dcac
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965287"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에 대한 데이터 보존 및 고객 데이터

고객 데이터는 Microsoft 365 및 Azure와 같은 Microsoft 온라인 서비스를 구독할 때 조직에서 제공 하는 주소에 따라 지리적 위치에 Azure AD에 저장 됩니다. 고객 데이터를 저장하는 위치에 대한 정보는 Microsoft 보안 센터의 [데이터를 어디에 배치하나요?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 섹션을 사용할 수 있습니다.

클라우드 기반 Azure Multi-Factor Authentication 및 Azure Multi-Factor Authentication 서버는 일부 개인 데이터와 조직 데이터를 처리하고 저장합니다. 이 문서에서는 저장되는 데이터 및 위치를 간략하게 설명합니다.

Azure Multi-Factor Authentication 서비스에는 미국, 유럽 및 아시아 태평양의 데이터 센터가 있습니다. 다음 활동은 명시 된 경우를 제외 하 고는 지역 데이터 센터에서 제외 됩니다.

* 전화 통화를 사용 하는 multi-factor authentication은 미국 데이터 센터에서 시작 되며 전역 공급자에 의해 라우팅됩니다.
* 유럽 또는 오스트레일리아와 같은 다른 지역의 범용 사용자 인증 요청은 현재 사용자의 위치를 기준으로 처리 됩니다.
* Microsoft Authenticator 앱을 사용 하는 푸시 알림은 현재 사용자의 위치를 기반으로 하는 지역 데이터 센터에서 처리 됩니다.
    * Apple 푸시 알림과 같은 장치 공급 업체 특정 서비스는 사용자의 위치 외부에 있을 수 있습니다.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에서 저장된 개인 데이터

개인 데이터는 특정 사용자와 연결된 사용자 수준 정보입니다. 다음 데이터 저장소에는 개인 정보가 포함되어 있습니다.

* 차단된 사용자
* 바이패스된 사용자
* Microsoft Authenticator 디바이스 토큰 변경 요청
* Multi-Factor Authentication 작업 보고서
* Microsoft Authenticator 활성화

이 정보는 90일 동안 보존됩니다.

Azure Multi-Factor Authentication은 사용자 이름, 전화 번호 또는 IP 주소와 같은 개인 데이터를 기록하지 않지만 사용자에 대한 Multi-Factor Authentication 시도를 식별하는 *UserObjectId*가 있습니다. 로그 데이터는 30일 동안 저장됩니다.

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Azure B2C 인증, NPS 확장 및 Windows Server 2016 또는 2019 AD FS 어댑터를 제외하고 Azure 퍼블릭 클라우드의 경우 다음 개인 데이터가 저장됩니다.

| 이벤트 유형                           | 데이터 저장소 유형 |
|--------------------------------------|-----------------|
| OATH 토큰                           | Multi-Factor Authentication 로그에서     |
| 단방향 SMS                          | Multi-Factor Authentication 로그에서     |
| 음성 통화                           | Multi-Factor Authentication 로그에서<br />Multi-Factor Authentication 작업 보고서 데이터 저장소<br />사기 행위를 보고한 경우 차단된 사용자 |
| Microsoft Authenticator 알림 | Multi-Factor Authentication 로그에서<br />Multi-Factor Authentication 작업 보고서 데이터 저장소<br />사기 행위를 보고한 경우 차단된 사용자<br />Microsoft Authenticator 디바이스 토큰이 변경될 때 변경 요청 |

> [!NOTE]
> Multi-Factor Authentication 작업 보고서 데이터 저장소는 인증 요청을 처리하는 지역과 관계 없이 모든 클라우드의 미국에 저장됩니다. Microsoft Azure 독일, 21Vianet에서 운영하는 Microsoft Azure 및 Microsoft Government 클라우드는 퍼블릭 클라우드 지역 데이터 저장소와 별도의 독립적인 데이터 저장소를 가지고 있지만 이 데이터는 항상 미국에 저장됩니다.

Microsoft Azure Government, Microsoft Azure 독일, 21Vianet에서 운영하는 Microsoft Azure, Azure B2C 인증, NPS 확장 및 Windows Server 2016 또는 2019 AD FS 어댑터의 경우 다음 개인 데이터가 저장됩니다.

| 이벤트 유형                           | 데이터 저장소 유형 |
|--------------------------------------|-----------------|
| OATH 토큰                           | Multi-Factor Authentication 로그에서<br />Multi-Factor Authentication 작업 보고서 데이터 저장소 |
| 단방향 SMS                          | Multi-Factor Authentication 로그에서<br />Multi-Factor Authentication 작업 보고서 데이터 저장소 |
| 음성 통화                           | Multi-Factor Authentication 로그에서<br />Multi-Factor Authentication 작업 보고서 데이터 저장소<br />사기 행위를 보고한 경우 차단된 사용자 |
| Microsoft Authenticator 알림 | Multi-Factor Authentication 로그에서<br />Multi-Factor Authentication 작업 보고서 데이터 저장소<br />사기 행위를 보고한 경우 차단된 사용자<br />Microsoft Authenticator 디바이스 토큰이 변경될 때 변경 요청 |

### <a name="multi-factor-authentication-server"></a>Multi-Factor Authentication 서버

Azure Multi-Factor Authentication 서버를 배포하고 실행하는 경우 다음 개인 데이터가 저장됩니다.

> [!IMPORTANT]
> 2019년 7월 1일부터 Microsoft는 더 이상 새 배포를 위한 Multi-Factor Authentication 서버를 제공하지 않습니다. 신규 사용자의 다단계 인증이 필요한 고객은 클라우드 기반 Azure Multi-Factor Authentication을 사용해야 합니다. 7월 1일 이전에 Multi-Factor Authentication 서버를 활성화한 기존 고객은 종전과 같이 최신 버전 및 이후 업데이트를 다운로드하고 활성화 자격 증명을 생성할 수 있습니다.

| 이벤트 유형                           | 데이터 저장소 유형 |
|--------------------------------------|-----------------|
| OATH 토큰                           | Multi-Factor Authentication 로그에서<br />Multi-Factor Authentication 작업 보고서 데이터 저장소 |
| 단방향 SMS                          | Multi-Factor Authentication 로그에서<br />Multi-Factor Authentication 작업 보고서 데이터 저장소 |
| 음성 통화                           | Multi-Factor Authentication 로그에서<br />Multi-Factor Authentication 작업 보고서 데이터 저장소<br />사기 행위를 보고한 경우 차단된 사용자 |
| Microsoft Authenticator 알림 | Multi-Factor Authentication 로그에서<br />Multi-Factor Authentication 작업 보고서 데이터 저장소<br />사기 행위를 보고한 경우 차단된 사용자<br />Microsoft Authenticator 디바이스 토큰이 변경될 때 변경 요청 |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에서 저장된 조직 데이터

조직 데이터는 구성 또는 환경 설치를 노출할 수 있는 테넌트 수준 정보입니다. 다음 Azure Portal Multi-Factor Authentication 페이지에서 테넌트 설정은 수신 전화 인증 요청에 대한 잠금 임계값 또는 호출자 ID 정보와 같은 조직 데이터를 저장할 수 있습니다.

* 계정 잠금
* 사기 행위 경고
* 공지
* 전화 통화 설정

Azure Multi-Factor Authentication 서버의 경우 다음 Azure Portal 페이지에 조직 데이터가 포함될 수 있습니다.

* 서버 설정
* 일회성 바이패스
* 캐싱 규칙
* Multi-Factor Authentication 서버 상태

## <a name="log-data-location"></a>로그 데이터 위치

로그 정보가 저장되는 위치는 처리되는 지역에 따라 다릅니다. 대부분의 지역에는 네이티브 Azure Multi-Factor Authentication 기능이 있으므로 로그 데이터는 Multi-Factor Authentication 요청을 처리하는 동일한 지역에 저장됩니다. 네이티브 Azure Multi-Factor Authentication을 지원하지 않는 지역에서 이러한 서비스는 미국 또는 유럽 지역에서 서비스되며, 로그 데이터는 Multi-Factor Authentication 요청을 처리하는 동일한 지역에 저장됩니다.

일부 핵심 인증 로그 데이터는 미국에만 저장됩니다. Microsoft Azure 독일 및 21Vianet에서 운영하는 Microsoft Azure는 항상 해당 클라우드에 저장됩니다. Microsoft Government 클라우드 로그 데이터는 항상 미국에 저장됩니다.

## <a name="next-steps"></a>다음 단계

클라우드 기반 Azure Multi-Factor Authentication 및 Azure Multi-Factor Authentication 서버에서 수집하는 사용자 정보에 대한 자세한 내용은 [Azure Multi-Factor Authentication 사용자 데이터 수집](howto-mfa-reporting-datacollection.md)을 참조하세요.
