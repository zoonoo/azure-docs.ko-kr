---
title: 관리형 HSM 로컬 RBAC 기본 제공 역할 - Azure Key Vault | Microsoft Docs
description: 사용자, 서비스 주체, 그룹 및 관리 ID에 할당할 수 있는 관리형 HSM 기본 제공 역할 개요
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 6b39c1d6fe2e84055e7c94dde076514d04a24f36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90992441"
---
# <a name="managed-hsm-local-rbac-built-in-roles"></a>관리형 HSM 로컬 RBAC 기본 제공 역할

관리형 HSM 로컬 RBAC에는 사용자, 서비스 주체, 그룹 및 관리 ID에 할당할 수 있는 몇 가지 기본 제공 역할이 있습니다. 보안 주체가 작업을 수행할 수 있도록 하려면 해당 작업을 수행할 수 있는 권한을 부여하는 역할을 할당해야 합니다. 이러한 모든 역할 및 작업을 통해 데이터 평면 작업에 대한 권한만 관리할 수 있습니다. 관리형 HSM 리소스에 대한 컨트롤 플레인 권한을 관리하려면(예: 새 관리형 HSM 생성 또는 기존 관리형 HSM 업데이트, 이동, 삭제) [Azure RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 사용해야 합니다.

## <a name="built-in-roles"></a>기본 제공 역할

|역할 이름|Description|ID|
|---|---|---|
|관리형 HSM 관리자| 모든 데이터 작업에 대한 전체 액세스 권한을 부여합니다.|a290e904-7015-4bba-90c8-60543313cdb4|
|관리형 HSM Crypto 책임자| 모든 키 관리 및 키 암호화 작업에 대한 전체 액세스 권한을 부여합니다.|515eb02d-2335-4d2d-92f2-b1cbdf9c3778|
|관리형 HSM Crypto 사용자|암호화 작업을 위한 키를 만들고 사용할 수 있는 액세스 권한을 부여합니다. 키를 영구적으로 삭제할 수 없습니다.|21dbd100-6940-42c2-9190-5d6cb909625b|
|관리형 HSM 정책 관리자| 역할 할당을 만들고 삭제할 수 있는 권한을 부여합니다.|4bd23610-cdcf-4971-bdee-bdc562cc28e4|
|관리형 HSM Crypto 감사자|키를 읽을 수 있는(사용하지 않음) 읽기 권한을 부여합니다.|2c18b078-7c48-4d3a-af88-5a3a1b3f82b3|
|관리형 HSM Crypto Service 암호화| 서비스 암호화를 위해 키를 사용할 수 있는 권한을 부여합니다. |33413926-3206-4cdd-b39a-83574fe37a17|
|관리형 HSM 백업| 단일 키 또는 전체 HSM 백업을 수행할 수 있는 권한을 부여합니다. |7b127d3c-77bd-4e3e-bbe0-dbb8971fa7f8|

## <a name="permitted-operations"></a>허용된 연산
> [!NOTE]  
> - 'X'는 역할이 데이터 작업을 수행할 수 있음을 나타냅니다. 빈 셀은 역할에 해당 데이터 작업을 수행할 수 있는 권한이 없음을 나타냅니다.
> - 모든 데이터 작업 이름에는 'Microsoft.KeyVault/managedHsm' 접두사가 있습니다. 이 접두사는 간결하게 하기 위해 아래 표에서 생략되어 있습니다.
> - 모든 역할 이름에는 간결성을 위해 아래 표에서 생략된 "Managed HSM" 접두사가 있습니다.

|데이터 작업 | 관리자 | 암호화 담당자 | Crypto 사용자 | 정책 관리자 | Crypto Service 암호화 | Backup | Crypto 감사자|
|---|---|---|---|---|---|---|---|
|**보안 도메인 관리**|
/securitydomain/download/action|<center>X</center>||||||
/securitydomain/upload/action|<center>X</center>||||||
/securitydomain/upload/read|<center>X</center>||||||
/securitydomain/transferkey/read|<center>X</center>||||||
|**키 관리**|
|/keys/read/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||<center>X</center>|
|/keys/write/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/create|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/delete|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/read/action|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/recover/action|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/delete|<center>X</center>|<center>X</center>|||||<center>X</center>|
|/keys/backup/action|<center>X</center>|<center>X</center>|<center>X</center>|||<center>X</center>|
|/keys/restore/action|<center>X</center>|<center>X</center>|||||
|/keys/export/action|<center>X</center>|<center>X</center>|||||
|/keys/import/action|<center>X</center>|<center>X</center>|||||
|**키 암호화 작업**|
|/keys/encrypt/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/decrypt/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/wrap/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||
|/keys/unwrap/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||
|/keys/sign/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/verify/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|**역할 관리**|
|/roleAssignments/delete/action|<center>X</center>|||<center>X</center>|||
|/roleAssignments/read/action|<center>X</center>|||<center>X</center>|||
|/roleAssignments/write/action|<center>X</center>|||<center>X</center>|||
|/roleDefinitions/read/action|<center>X</center>|||<center>X</center>|||
|**백업/복원 관리**|
|/backup/start/action|<center>X</center>|||||<center>X</center>|
|/backup/status/action|<center>X</center>|||||<center>X</center>|
|/restore/start/action|<center>X</center>||||||
|/restore/status/action|<center>X</center>||||||
||||||||

## <a name="next-steps"></a>다음 단계

- [Azure RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md) 개요를 참조하세요.
- [관리형 HSM 역할 관리](role-management.md)에 대한 자습서를 참조하세요.
