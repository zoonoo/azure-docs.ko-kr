---
title: 보안 개요
description: Azure Arc 사용 서버에 대 한 보안 정보입니다.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: be79be3030af76425b54fd683784d0e216ac2cf5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329043"
---
# <a name="azure-arc-for-servers-security-overview"></a>서버용 Azure Arc 보안 개요

이 문서에서는 기업에 Azure Arc 사용 서버를 배포 하기 전에 평가 해야 하는 보안 구성과 고려 사항을 설명 합니다.

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

각 Azure Arc 사용 서버에는 Azure 구독 내 리소스 그룹의 일부로 관리 되는 id가 있습니다 .이 id는 온-프레미스 또는 다른 클라우드 환경에서 실행 되는 서버를 나타냅니다. 표준 [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)에서이 리소스에 대 한 액세스를 제어 합니다. Azure Portal의 [**Access Control (IAM)**](../../role-based-access-control/role-assignments-portal.md#access-control-iam) 페이지에서 Azure Arc 사용 서버에 대 한 액세스 권한이 있는 사용자를 확인할 수 있습니다.

:::image type="content" source="./media/security-overview/access-control-page.png" alt-text="Azure Arc 사용 서버 액세스 제어" border="false" lightbox="./media/security-overview/access-control-page.png":::

사용자 및 응용 프로그램에서 리소스에 대 한 액세스 [권한이 부여 된](../../role-based-access-control/built-in-roles.md#contributor) 사용자 및 응용 프로그램은 컴퓨터에 [확장](manage-vm-extensions.md) 을 배포 하거나 삭제 하는 등 리소스를 변경할 수 있습니다. 확장에는 권한 있는 컨텍스트에서 실행 되는 임의의 스크립트가 포함 될 수 있으므로 Azure 리소스의 모든 참가자가 서버의 간접 관리자가 될 수 있습니다.

**Azure에 연결 된 컴퓨터 온 보** 딩 역할은 대규모 온 보 딩에 사용할 수 있으며 azure에서 새로운 Arc 사용 서버를 읽거나 만들 수 있습니다. 이미 등록 된 서버를 삭제 하거나 확장을 관리 하는 데 사용할 수 없습니다. 모범 사례로, 대규모 컴퓨터를 등록 하는 데 사용 되는 Azure Active Directory (Azure AD) 서비스 사용자 에게만이 역할을 할당 하는 것이 좋습니다.

**Azure 연결 된 컴퓨터 리소스 관리자** 역할의 구성원 인 사용자는 컴퓨터를 읽고, 수정 하 고, 다시 등록 하 고, 삭제할 수 있습니다. 이 역할은 Arc 사용 서버에 대 한 관리를 지원 하지만 리소스 그룹 또는 구독의 다른 리소스에 대해서는 지원 하지 않습니다.

## <a name="agent-security-and-permissions"></a>에이전트 보안 및 사용 권한

Windows에서 Azure 연결 된 컴퓨터 에이전트 (azcmagent)를 관리 하려면 사용자 계정이 로컬 관리자 그룹의 구성원 이어야 합니다. Linux에는 루트 액세스 권한이 있어야 합니다.

Azure 연결 된 컴퓨터 에이전트는 사용자의 컴퓨터에서 실행 되는 세 가지 서비스로 구성 됩니다.

* 하이브리드 Instance Metadata Service (himds) 서비스는 원호의 모든 핵심 기능을 담당 합니다. 여기에는 Azure에 하트 비트 보내기가 포함 되며, 다른 앱에 대 한 로컬 인스턴스 메타 데이터 서비스를 노출 하 여 컴퓨터의 Azure 리소스 ID에 대해 알아보고, 다른 Azure 서비스에 인증 하기 위해 Azure AD 토큰을 검색 합니다. 이 서비스는 Windows의 권한 없는 가상 서비스 계정 및 Linux의 **himds** 사용자로 실행 됩니다.

* 게스트 구성 서비스 (GCService)는 컴퓨터에서 Azure Policy를 평가 하는 일을 담당 합니다.

* ExtensionService (게스트 구성 확장 서비스)는 컴퓨터에서 확장 (에이전트, 스크립트 또는 기타 소프트웨어)을 설치, 업데이트 및 삭제 하는 일을 담당 합니다.

게스트 구성 및 확장 서비스는 Windows에서 로컬 시스템으로 실행 되 고 Linux의 루트로 실행 됩니다.

## <a name="using-a-managed-identity-with-arc-enabled-servers"></a>Arc 사용 서버에서 관리 되는 id 사용

기본적으로 Arc에서 사용 하는 Azure Active Directory 시스템 할당 id는 Azure에서 Arc 사용 서버의 상태를 업데이트 하는 데만 사용할 수 있습니다. 예를 들어 *마지막으로 표시* 된 하트 비트 상태입니다. 서버 응용 프로그램에서 시스템 할당 id를 사용 하 여 다른 Azure 서비스에 액세스 하는 경우 필요에 따라 id에 추가 역할을 할당할 수 있습니다.

컴퓨터에서 실행 되는 응용 프로그램에서 하이브리드 Instance Metadata Service에 액세스할 수 있지만, 권한이 부여 된 응용 프로그램만 시스템 할당 id에 대 한 Azure AD 토큰을 요청할 수 있습니다. 토큰 URI에 처음 액세스 하려고 할 때 서비스는 신뢰할 수 있는 호출자만 읽을 수 있는 파일 시스템의 위치에 임의로 생성 된 암호화 blob을 생성 합니다. 그런 다음 호출자는 파일을 읽고 (적절 한 권한이 있음을 증명) 인증 헤더의 파일 내용으로 요청을 다시 시도 하 여 Azure AD 토큰을 성공적으로 검색 합니다.

* Windows에서 호출자는 로컬 **관리자** 그룹 또는 **하이브리드 에이전트 확장 응용 프로그램** 그룹의 구성원 이어야 blob을 읽을 수 있습니다.

* Linux에서 호출자는 blob을 읽을 **himds** 그룹의 멤버 여야 합니다.

## <a name="using-disk-encryption"></a>디스크 암호화 사용

Azure 연결 된 컴퓨터 에이전트는 공개 키 인증을 사용 하 여 Azure 서비스와 통신 합니다. Azure Arc에 서버를 등록 한 후에는 개인 키가 디스크에 저장 되 고 에이전트가 Azure와 통신할 때마다 사용 됩니다. 도난당 한 경우 개인 키를 다른 서버에서 사용 하 여 서비스와 통신 하 고 원래 서버 처럼 작동할 수 있습니다. 여기에는 시스템 할당 id에 대 한 액세스 권한 및 id가 액세스할 수 있는 모든 리소스가 포함 됩니다. 개인 키 파일은 **himds** 계정 액세스를 통해서만 읽을 수 있도록 보호 됩니다. 오프 라인 공격을 방지 하려면 서버의 운영 체제 볼륨에서 전체 디스크 암호화 (예: BitLocker, dm-자리에 지 등)를 사용 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

여러 하이브리드 머신에서 Arc 지원 서버를 평가하거나 활성화하기 전에 [연결된 머신 에이전트 개요](agent-overview.md)를 검토하여 요구 사항, 에이전트에 대한 기술 세부 정보 및 배포 방법을 이해하세요.
