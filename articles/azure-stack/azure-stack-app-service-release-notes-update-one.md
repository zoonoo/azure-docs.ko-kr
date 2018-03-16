---
title: "Azure 스택 업데이트 1에서 응용 프로그램 서비스 | Microsoft Docs"
description: "Azure 스택 앱 서비스에 대 한 업데이트 하나에 포함 된 내용에 대 한 자세한 내용은 알려진된 문제 및 업데이트를 다운로드 하는 위치입니다."
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 0c33c8fdefbb27ba8414e58bed1b42ee7aaba88a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="app-service-on-azure-stack-update-one-release-notes"></a>Azure 스택 앱 서비스 업데이트 1 릴리스 정보

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

이러한 릴리스 정보에 개선 사항 및 Azure 앱 서비스의 수정 Azure 스택 업데이트 1과 관련 된 알려진된 문제에 설명 합니다. 알려진된 문제를 직접 배포, 업데이트 프로세스 및 문제 (설치 후) 빌드와 관련 된 문제 나뉩니다.

> [!IMPORTANT]
> Azure 스택 통합 시스템 1802 업데이트를 적용 하거나 Azure 앱 서비스를 배포 하기 전에 최신 Azure 스택 개발 키트를 배포 합니다.
>
>

## <a name="build-reference"></a>빌드 참조

Azure 스택 업데이트 1 빌드 번호에 앱 서비스는 **69.0.13698.9**

### <a name="prerequisites"></a>필수 조건

> [!IMPORTANT]
> Azure 스택 앱 서비스를 azure에서 이제 필요는 [3 주체 와일드 카드 인증서](azure-stack-app-service-before-you-get-started.md#get-certificates) 처리 하는 방식에는 SSO에 대 한 Kudu 이제 Azure 앱 서비스에서의 향상으로 인해 합니다.  새 주제는 * * *. sso.appservice.<region>합니다. <domainname>.<extension>**
>
>

참조는 [시작 하기 전에 설명서](azure-stack-app-service-before-you-get-started.md) 배포가 시작 되기 전에 합니다.

### <a name="new-features-and-fixes"></a>새 기능 및 수정

Azure 스택 업데이트 1에서 azure 앱 서비스는 다음과 같은 향상 된 기능 및 수정 합니다.

- **높은 가용성의 Azure 앱 서비스** -The Azure 스택 1802 사용 하도록 설정 하는 업데이트 작업에 배포할 오류 도메인입니다.  따라서 응용 프로그램 서비스 인프라는 오류 도메인 배포 될 내결함성이 있는 될 수 있습니다.  하지만 기본적으로 Azure 스택 1802 하기 전에 완료 하는 배포에 적용 되 고 업데이트를 참조 Azure 앱 서비스의 모든 새 배포가이 기능이 적용 됩니다는 [응용 프로그램 서비스 오류 도메인 설명서](azure-stack-app-service-fault-domain-update.md)

- **기존 가상 네트워크에 배포** -고객은 기존 가상 네트워크 내에서 이제 Azure 스택 앱 서비스를 배포할 수 있습니다.  기존 가상 네트워크에 배포 고객을 SQL Server 및 개인 포트를 통해 Azure 앱 서비스에 필요한 파일 서버에 연결할 수 있습니다.  그러나 기존 가상 네트워크를 배포 하려면 배포 중 고객이 선택할 수 [앱 서비스에서 사용 하기 위해 서브넷 만들어야](azure-stack-app-service-before-you-get-started.md#virtual-network) 배포 하기 전에.

- 업데이트를 **응용 프로그램 서비스 테 넌 트, 관리자, 함수 포털 및 Kudu 도구**합니다.  Azure 스택 포털 SDK 버전와 일치 합니다.

- **다음 응용 프로그램 프레임 워크 및 도구에 대 한 업데이트**:
    - 추가 **.NET 2.0 핵심** 지원
    - 추가 **Node.JS** 버전:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - 추가 **NPM** 버전:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - 추가 **PHP** 업데이트:
        - 5.6.32
        - 7.0.26 (x86 및 x64)
        - 7.1.12 (x86 및 x64)
    - 업데이트 **Windows 용 Git** v 2.14.1로
    - 업데이트 **Mercurial** 첫 v4.5.0에

  - 에 대 한 지원을 추가 **HTTPS만** 앱 서비스 테 넌 트 포털의 사용자 지정 도메인 기능 내의 기능입니다. 

  - Azure 함수에 대 한 사용자 지정 저장소 선택에서 저장소 연결의 추가 유효성 검사 

#### <a name="fixes"></a>수정 프로그램

- 고객 액세스 거부 오류 메시지가 응용 프로그램 서비스 설치 관리자에서 폴더를 열 때 더 이상 받지 않습니다 오프 라인 배포 패키지를 만들 때

- 앱 서비스 테 넌 트 포털의 사용자 지정 도메인 기능에서 작업할 때 문제를 해결 합니다.

- 설치 하는 동안 예약 된 관리자 이름을 사용 하는 고객을 방지

- 앱 서비스 배포를 사용 하도록 설정 **도메인에 가입** 파일 서버

- Azure 스택 루트가의 향상 된 검색 스크립트에서 인증서 및 응용 프로그램 서비스 설치 관리자에서 루트 인증서 유효성을 검사 합니다.

- Microsoft.Web 네임 스페이스에 포함 된 해당 리소스를 삭제 하는 경우 구독을 Azure 리소스 관리자에 반환 되는 고정된 잘못 된 상태.

### <a name="known-issues-with-the-deployment-process"></a>배포 프로세스의 알려진된 문제

- Azure 스택 업데이트 1에서 Azure 앱 서비스의 배포에 대 한 알려진된 문제가 없는지 합니다.

### <a name="known-issues-with-the-update-process"></a>업데이트 프로세스의 알려진된 문제

- Azure 스택 업데이트 1에서 Azure 앱 서비스의 업데이트에 대 한 알려진된 문제가 없는지 합니다.

### <a name="known-issues-post-installation"></a>알려진된 문제 (설치 후)

- Azure 스택 업데이트 1에서 Azure 앱 서비스의 설치에 대 한 알려진된 문제가 없는지 합니다.

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure 스택 앱 서비스를 Azure 운영 클라우드 관리자에 대 한 알려진된 문제

설명서에서 참조는 [Azure 스택 1802 릴리스 정보](azure-stack-update-1802.md)

## <a name="see-also"></a>참고 항목

- Azure 앱 서비스의 개요를 참조 하십시오. [Azure 앱 서비스에 대 한 Azure 스택 개요](azure-stack-app-service-overview.md)합니다.
- Azure 스택 앱 서비스 배포를 준비 하는 방법에 대 한 자세한 내용은 참조 [Azure 스택 앱 서비스를 시작 하기 전에](azure-stack-app-service-before-you-get-started.md)합니다.
