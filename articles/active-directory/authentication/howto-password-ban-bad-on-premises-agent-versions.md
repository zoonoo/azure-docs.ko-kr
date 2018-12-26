---
title: 온-프레미스 Azure AD 암호 보호 에이전트 버전 릴리스 기록
description: 문서 버전 릴리스 및 동작 변경 기록
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 11/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 89d64a28d2fe43464995e434c9f3807047b29492
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913639"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>미리 보기: Azure AD 암호 보호 에이전트 버전 기록

|     |
| --- |
| Azure AD 암호 보호는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

## <a name="12250"></a>1.2.25.0

릴리스 날짜: 2018/11/01

수정 사항:

* DC 에이전트 및 프록시 서비스가 인증서 트러스트 오류로 인해 더 이상 실패하지 않아야 합니다.
* DC 에이전트 및 프록시 서비스에는 FIPS 규격 머신에 대한 추가 수정 프로그램이 있습니다.
* 프록시 서비스는 이제 TLS 1.2 전용 네트워킹 환경에서 제대로 작동합니다.
* 사소한 성능 및 견고성 수정
* 향상된 로깅

변경 내용:

* 이제 프록시 서비스의 최소 필수 OS 수준은 Windows Server 2012 R2입니다. DC 에이전트 서비스의 최소 필수 OS 수준은 Windows Server 2012에서 유지됩니다.
* 암호 유효성 검사 알고리즘은 확장된 문자 정규화 테이블을 사용합니다. 이로 인해 이전 버전에서 허용되었던 암호가 거부될 수 있습니다.

## <a name="12100"></a>1.2.10.0

릴리스 날짜: 2018/8/17

수정 사항:

* Register-AzureADPasswordProtectionProxy 및 Register-AzureADPasswordProtectionForest는 이제 다단계 인증을 지원합니다.
* Register-AzureADPasswordProtectionProxy에는 암호화 오류를 방지하기 위해 도메인에서 WS2012 이상의 도메인 컨트롤러가 필요합니다.
* DC 에이전트 서비스는 시작 시 Azure에서 새 암호 정책을 요청하는 경우 더 안정적입니다.
* DC 에이전트 서비스는 필요한 경우 1시간마다 Azure의 새 암호 정책을 요청하지만 이제 임의로 선택된 시작 시간에 수행됩니다.
* DC 에이전트 서비스가 복제본으로 승격되기 전에 서버에 설치되는 경우 더 이상 새 DC 알림에서 무한 지연을 발생시키지 않습니다.
* 이제 DC 에이전트 서비스는 "Windows Server Active Directory에 대한 암호 보호를 사용하도록 설정" 구성 설정을 유지합니다.
* 이제 DC 에이전트 및 프록시 설치 관리자는 모두 이후 버전으로 업그레이드하는 경우 현재 위치 업그레이드를 지원합니다.

> [!WARNING]
> 1.1.10.3 버전에서 현재 위치 업그레이드는 지원되지 않으며 설치 오류가 발생합니다. 1.2.10 버전 이상으로 업그레이드하려면 먼저 DC 에이전트 및 프록시 서비스 소프트웨어를 완전히 제거한 다음, 처음부터 새 버전을 설치합니다. Azure AD 암호 보호 프록시 서비스를 다시 등록해야 합니다.  포리스트를 다시 등록할 필요가 없습니다.

> [!NOTE]
> DC 에이전트 소프트웨어의 현재 위치 업그레이드에는 다시 부팅이 필요합니다.

* 이제 DC 에이전트 및 프록시 서비스는 FIPS 규격 알고리즘만 사용하도록 구성된 서버에서 실행되도록 지원합니다.
* 사소한 성능 및 견고성 수정
* 향상된 로깅

## <a name="11103"></a>1.1.10.3

릴리스 날짜: 2018/6/15

초기 공개 미리 보기 릴리스

## <a name="next-steps"></a>다음 단계

[Azure AD 암호 보호 배포](howto-password-ban-bad-on-premises-deploy.md)
