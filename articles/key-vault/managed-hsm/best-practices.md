---
title: Azure Key Vault 관리 HSM을 사용 하는 모범 사례
description: 이 문서에서는 사용 하기 위한 몇 가지 모범 사례에 대해 설명 Key Vault
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 7a30a7ab6689b602bc9ad4f696a6fe54c80f2151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90996855"
---
# <a name="best-practices-when-using-managed-hsm"></a>관리 되는 HSM 사용에 대 한 모범 사례

## <a name="control-access-to-your-managed-hsm"></a>관리 되는 HSM에 대 한 액세스 제어

관리 되는 HSM은 암호화 키를 보호 하는 클라우드 서비스입니다. 이러한 키는 중요 하 고 비즈니스에 중요 한 것 이므로 권한 있는 응용 프로그램 및 사용자만 허용 하 여 관리 되는 Hsm에 대 한 액세스를 보호 해야 합니다. 이 [문서](access-control.md) 에서는 액세스 모델의 개요를 제공 합니다. 인증 및 권한 부여 및 역할 기반 액세스 제어에 대해 설명 합니다.
- 개인에 게 관리자 역할을 할당 하는 대신 HSM 관리자에 대 한 [Azure Active Directory 보안 그룹](../../active-directory/fundamentals/active-directory-manage-groups.md) 을 만듭니다. 이렇게 하면 개별 계정이 삭제 될 경우 "관리 잠금"이 차단 됩니다.
- 관리 그룹, 구독, 리소스 그룹 및 관리 되는 Hsm에 대 한 액세스 잠금-Azure RBAC를 사용 하 여 관리 그룹, 구독 및 리소스 그룹에 대 한 액세스 제어
- [관리 되는 HSM 로컬 RBAC](access-control.md#data-plane-and-managed-hsm-local-rbac) 를 사용 하 여 키 역할 할당 당 만들기
- 최소 권한 액세스 보안 주체를 사용 하 여 역할 할당

## <a name="choose-regions-that-support-availability-zones"></a>가용성 영역을 지 원하는 지역 선택

- 최상의 고가용성 및 영역 복원 력을 보장 하려면 [가용성 영역](../../availability-zones/az-overview.md) 지원 되는 Azure 지역을 선택 합니다. 이러한 지역은 Azure Portal에 "권장 영역"으로 표시 됩니다.

## <a name="backup"></a>Backup

- HSM의 정기 백업을 수행 해야 합니다. HSM 수준 및 특정 키에 대 한 백업을 수행할 수 있습니다. 

## <a name="turn-on-logging"></a>로깅 켜기

- HSM에 대 한 [로깅을 설정](logging.md) 합니다. 또한 경고를 설정 합니다.

## <a name="turn-on-recovery-options"></a>복구 옵션 설정

- [일시 삭제](../general/soft-delete-overview.md) 는 기본적으로 설정 되어 있습니다.
- 일시 삭제가 설정 된 후에도 HSM을 강제로 삭제 하지 않도록 보호 하려면 제거 보호를 설정 합니다.

## <a name="next-steps"></a>다음 단계

- 전체 HSM 백업/복원에 대 한 자세한 내용은 [전체 백업/복원](backup-restore.md) 을 참조 하세요.
- Azure Monitor를 사용 하 여 로깅을 구성 하는 방법을 알아보려면 [관리 되는 HSM 로깅](logging.md) 을 참조 하세요.
- 키 관리를 위해 [관리 되는 HSM 키 관리](key-management.md) 를 참조 하세요.
- 역할 할당 관리에 대 한 [관리 되는 HSM 역할 관리](role-management.md) 를 참조 하세요.
