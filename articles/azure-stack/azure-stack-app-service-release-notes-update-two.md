---
title: Azure Stack에서 app Service 업데이트 2 릴리스 정보 | Microsoft Docs
description: Azure Stack에서 App Service에 대 한 두의 알려진된 문제 및 업데이트를 다운로드 하는 위치 업데이트에 포함 된 내용에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: anwestg
ms.reviewer: sethm
ms.openlocfilehash: f40d88df7a46c73981b6f20bee0b119743c08257
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714494"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>App Service에서 Azure Stack 업데이트 2 릴리스 정보

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

이러한 릴리스 정보는 Azure Stack 업데이트 2 알려진된 문제에는 향상 된 기능 및 Azure App Service에서 수정 설명 합니다. 알려진된 문제를 직접 배포, 업데이트 프로세스 및 빌드 (사후 설치)를 사용 하 여 문제에 관련 된 문제 나뉩니다.

> [!IMPORTANT]
> Azure Stack 통합 시스템 1804 업데이트를 적용 하거나 Azure 앱 서비스 1.2를 배포 하기 전에 최신 Azure Stack 개발 키트를 배포 합니다.
>
>

## <a name="build-reference"></a>빌드 참조

Azure Stack Update 2 빌드 번호에 App Service는 **72.0.13698.10**

### <a name="prerequisites"></a>필수 조건

> [!IMPORTANT]
> 이제 Azure Stack에서 Azure App Service의 새 배포에 필요는 [세 주체 와일드 카드 인증서](azure-stack-app-service-before-you-get-started.md#get-certificates) 처리 하는 방식에는 SSO kudu 이제 Azure App Service에서의 향상으로 인해 합니다. 새 제목은  **\*. sso.appservice.\< 지역\>.\< domainname\>.\< 확장\>**
>
>

참조 된 [하기 전에 시작 설명서](azure-stack-app-service-before-you-get-started.md) 배포가 시작 되기 전에 합니다.

### <a name="new-features-and-fixes"></a>새 기능 및 수정

Azure Stack 업데이트 2의 azure App Service는 다음과 같은 향상 된 기능 및 수정 합니다.

- 업데이트 **테 넌 트 앱 서비스, 관리자의 경우 함수 포털 및 Kudu 도구**합니다. Azure Stack 포털 SDK 버전과 일치 합니다.

- 안정성 및 오류 일반적인 문제를 쉽게 진단 사용 메시지를 개선 하기 위해 핵심 서비스를 업데이트 합니다.

- **다음 응용 프로그램 프레임 워크 및 도구에 대 한 업데이트**:
  - 추가.Net Framework 4.7.1
  - 추가 **Node.JS** 버전:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - 추가 **NPM** 버전:
    - 5.6.0
  - .NET Core 업데이트 공용 클라우드에서 Azure App Service를 사용 하 여 일관 되도록 구성 합니다.
  - 업데이트 된 Kudu

- 배포의 자동 교환 슬롯 기능 사용- [자동 교환 구성](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap)

- 프로덕션 기능 사용-에서 테스트 [프로덕션에서 테스트 소개](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- Azure Functions 프록시 사용- [Azure Functions 프록시 사용](https://docs.microsoft.com/azure/azure-functions/functions-proxies)

- App Service 관리 확장 UX 지원 추가.
  - 비밀 회전
  - 인증서 회전
  - 시스템 자격 증명 회전
  - 연결 문자열 회전

### <a name="known-issues-post-installation"></a>알려진된 문제 (사후 설치)

- 작업자는 기존 가상 네트워크에 App Service 배포 되 고 파일 서버 에서만 사용 가능 개인 네트워크의 경우 파일 서버에 연결할 수 없습니다.

기존 가상 네트워크와 파일 서버에 연결할 내부 IP 주소에 배포 하기로 선택한 경우 아웃 바운드 보안 규칙을 추가 하 있습니다 작업자 서브넷과 파일 서버 간에 SMB 트래픽을 사용 하도록 설정 해야 합니다. 이렇게 하려면 관리 포털에서 WorkersNsg 이동한 다음 속성을 사용 하 여 아웃 바운드 보안 규칙을 추가 합니다.
 * 원본: 모두
 * 원본 포트 범위: *
 * 대상: IP 주소
 * 대상 IP 주소 범위: 파일 서버에 대 한 Ip의 범위
 * 대상 포트 범위: 445
 * 프로토콜: TCP
 * 작업: 허용
 * 우선 순위: 700
 * 이름: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure App Service on Azure Stack을 운영 하는 클라우드 관리자에 대 한 알려진된 문제

설명서를 참조 합니다 [Azure Stack 1804 릴리스 정보](azure-stack-update-1804.md)

## <a name="next-steps"></a>다음 단계

- Azure App Service의 개요를 보려면 [Azure App Service에서 Azure Stack 개요](azure-stack-app-service-overview.md)합니다.
- Azure Stack에서 App Service 배포를 준비 하는 방법에 대 한 자세한 내용은 참조 하세요. [App Service on Azure Stack을 사용 하 여 시작 하기 전에](azure-stack-app-service-before-you-get-started.md)입니다.
