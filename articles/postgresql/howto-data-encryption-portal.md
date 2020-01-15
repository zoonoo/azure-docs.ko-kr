---
title: 포털을 사용 하 여 Azure Database for PostgreSQL 단일 서버에 대 한 데이터 암호화
description: Azure Portal를 사용 하 여 Azure Database for PostgreSQL 단일 서버에 대 한 데이터 암호화를 설정 하 고 관리 하는 방법을 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: c836139047b83220e571842b6f365ab568f81e93
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941683"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-using-portal"></a>포털을 사용 하 여 Azure Database for PostgreSQL 단일 서버에 대 한 데이터 암호화

이 문서에서는 Azure Database for PostgreSQL 단일 서버에 대 한 데이터 암호화를 설정 하는 Azure Portal 사용 하도록를 설정 하 고 관리 하는 방법을 설명 합니다.

## <a name="prerequisites-for-cli"></a>CLI용 필수 구성 요소

* Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
* 고객 관리 키에 사용할 Azure Key Vault 및 키를 만듭니다.
* 키 자격 증명 모음에는 고객이 관리 하는 키로 사용 하는 다음과 같은 속성이 있어야 합니다.
  * [일시 삭제](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [보호 된 데이터 삭제](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* 키에는 고객 관리 키에 사용할 다음 특성이 있어야 합니다.
  * 만료 날짜 없음
  * 사용 안 함 없음
  * _Get_, _wrap 키_및 _래핑 해제 키_ 작업을 수행할 수 있습니다.

## <a name="setting-the-right-permissions-for-key-operations"></a>키 작업에 대 한 올바른 사용 권한 설정

1. Azure Key Vault에서 **액세스 정책을**선택한 다음 **액세스 정책 추가**를 선택 합니다.

   ![액세스 정책 개요](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. **키 사용 권한**아래에서 **가져오기**, **래핑**, **래핑** 해제 및 PostgreSQL 서버의 이름인 **보안 주체**를 선택 합니다. 기존 보안 주체 목록에서 서버 보안 주체를 찾을 수 없는 경우 처음으로 데이터 암호화를 설정 하 여이를 등록 해야 합니다. 그러면 오류가 발생 합니다.  

   ![액세스 정책 개요](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. 설정을 **저장**합니다.

## <a name="setting-data-encryption-for-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 단일 서버에 대 한 데이터 암호화 설정

1. **Azure Database for PostgreSQL**에서 **데이터 암호화** 를 선택 하 여 고객 관리 키 설정을 설정 합니다.

   ![데이터 암호화 설정](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. **Key Vault** 및 **키** 쌍을 선택 하거나 **키 식별자**를 전달할 수 있습니다.

   ![설정 Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. 설정을 **저장**합니다.

4. 모든 파일 ( **임시 파일**포함)이 전체 암호화 되었는지 확인 하려면 서버를 **다시 시작** **해야**합니다.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>데이터 암호화가 사용 하도록 설정 된 서버의 복제본 복원 또는 만들기

Key Vault에 저장 된 고객의 관리 키를 사용 하 여 Azure Database for PostgreSQL 단일 서버를 암호화 한 후에는 로컬 또는 지역 복원 작업 또는 복제본 (로컬/지역 간) 작업을 수행 하는 동안 새로 생성 된 서버 복사본을 만듭니다. 따라서 암호화 된 PostgreSQL 서버의 경우 다음 단계에 따라 암호화 된 복원 된 서버를 만들 수 있습니다.

1. 서버에서 **개요**를 선택 하 고 **복원**을 선택 합니다.

   ![시작-복원](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   또는 복제를 사용 하는 서버의 경우 **설정** 머리글 아래에 다음과 같이 **복제**를 선택 합니다.

   ![시작-복제본](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. 복원 작업이 완료 되 면 새로 생성 된 서버는 주 서버 키로 암호화 된 데이터입니다. 그러나 서버에서 기능 및 옵션을 사용할 수 없으며 서버가 **액세스할 수** 없는 상태로 표시 됩니다. 이 동작은 새 서버의 id에 Key Vault에 대 한 액세스 권한이 아직 제공 되지 않았기 때문에 데이터 조작을 방지 하기 위해 설계 되었습니다.

   ![서버에 액세스할 수 없음 표시](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. 액세스할 수 없는 상태를 수정 하려면 복원 된 서버에서 키의 유효성을 다시 검사 해야 합니다. **데이터 암호화** 창을 선택 하 고 **키 다시 유효성** 검사 단추를 선택 합니다.

   > [!NOTE]
   > 새 서버의 서비스 주체에 게 키 자격 증명 모음에 대 한 액세스 권한을 부여 해야 하기 때문에 첫 번째 유효성 재검사 시도는 실패 합니다. 서비스 주체를 생성 하려면 오류를 제공 하지만 서비스 주체를 생성 하는 **키 다시 유효성**검사를 선택 합니다. 그 후에는 위의 [섹션 2에 있는](#setting-the-right-permissions-for-key-operations) 단계를 참조 하세요.

   ![서버 유효성 다시 검사](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Key Vault에 대 한 새 서버에 대 한 액세스 권한을 부여 해야 합니다.

4. 서비스 주체를 등록 한 후에는 키의 유효성을 다시 검사 해야 하며 서버에서 정상적인 기능을 다시 시작 해야 합니다.

   ![일반 서버 복원 됨](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>다음 단계

 데이터 암호화에 대해 자세히 알아보려면 [Azure 데이터 암호화 란?](concepts-data-encryption-postgresql.md)을 참조 하세요.
