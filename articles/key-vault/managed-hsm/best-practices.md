---
title: Azure Key Vault 관리형 HSM을 사용하는 모범 사례
description: 이 문서에서는 Key Vault를 사용하는 몇 가지 모범 사례를 설명합니다.
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 7a30a7ab6689b602bc9ad4f696a6fe54c80f2151
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90996855"
---
# <a name="best-practices-when-using-managed-hsm"></a>관리형 HSM을 사용하는 경우의 모범 사례

## <a name="control-access-to-your-managed-hsm"></a>관리형 HSM에 대한 액세스 제어

관리형 HSM은 암호화 키를 보호하는 클라우드 서비스입니다. 키는 민감하고 업무상 중요하므로 권한 있는 애플리케이션과 사용자만 허용하여 관리형 HSM에 대한 액세스를 보호해야 합니다. 이 [문서](access-control.md)에서는 액세스 모델을 개괄적으로 설명합니다. 인증 및 권한 부여와 역할 기반 액세스 제어에 대해 설명합니다.
- 개인에게 관리자 역할을 할당하는 대신 HSM 관리자를 위한 [Azure Active Directory 보안 그룹](../../active-directory/fundamentals/active-directory-manage-groups.md)을 만듭니다. 이렇게 하면 개별 계정이 삭제될 경우 “관리 잠금”을 방지할 수 있습니다.
- 관리 그룹, 구독, 리소스 그룹, 관리형 HSM에 대한 액세스 잠금 - Azure RBAC를 사용하여 관리 그룹, 구독, 리소스 그룹에 대한 액세스 제어
- [관리형 HSM 로컬 RBAC](access-control.md#data-plane-and-managed-hsm-local-rbac)를 사용하여 키별 역할 할당 만들기
- 최소 권한 액세스 보안 주체를 사용하여 역할 할당

## <a name="choose-regions-that-support-availability-zones"></a>가용성 영역을 지원하는 지역 선택

- 뛰어난 성능의 고가용성 및 영역 복원력을 보장하려면 [가용성 영역](../../availability-zones/az-overview.md)이 지원되는 Azure 지역을 선택합니다. 해당 지역은 Azure Portal에서 “권장 지역”으로 표시됩니다.

## <a name="backup"></a>Backup

- HSM을 정기적으로 백업해야 합니다. HSM 수준에서 특정 키에 대해 백업을 수행할 수 있습니다. 

## <a name="turn-on-logging"></a>로깅 켜기

- HSM에 대한 [로깅을 켭니다](logging.md). 또한 경고를 설정합니다.

## <a name="turn-on-recovery-options"></a>복구 옵션 설정

- [일시 삭제](../general/soft-delete-overview.md)는 기본적으로 켜져 있습니다.
- 일시 삭제를 켠 후에도 HSM이 강제 삭제되지 않도록 보호하려면 제거 방지를 켭니다.

## <a name="next-steps"></a>다음 단계

- 전체 HSM 백업/복원에 대한 자세한 내용은 [전체 백업/복원](backup-restore.md)을 참조하세요.
- Azure Monitor를 사용하여 로깅을 구성하는 방법을 알아보려면 [관리형 HSM 로깅](logging.md)을 참조하세요.
- 키 관리에 대해서는 [관리형 HSM 키 관리](key-management.md)를 참조하세요.
- 역할 할당을 관리하는 방법은 [관리형 HSM 역할 관리](role-management.md)를 참조하세요.
