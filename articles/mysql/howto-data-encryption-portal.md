---
title: 데이터 암호화 - Azure 포털 - MySQL용 Azure 데이터베이스
description: Azure 포털을 사용하여 MySQL용 Azure 데이터베이스에 대한 데이터 암호화를 설정하고 관리하는 방법을 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 9d1e89919647d9d94b287618da2f9a77278425a5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459086"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Azure 포털을 사용 하 여 MySQL에 대 한 Azure 데이터베이스에 대 한 데이터 암호화

Azure 포털을 사용하여 MySQL용 Azure 데이터베이스에 대한 데이터 암호화를 설정하고 관리하는 방법을 알아봅니다.

## <a name="prerequisites-for-azure-cli"></a>Azure CLI에 대한 필수 구성 조건

* Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
* Azure 키 자격 증명 모음에서 고객 관리 키에 사용할 키 자격 증명 모음및 키를 만듭니다.
* 키 자격 증명 모음에는 고객 관리 키로 사용할 다음 속성이 있어야 합니다.
  * [소프트 삭제](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [보호된 제거](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* 고객 관리 키로 사용할 키에는 다음 특성이 있어야 합니다.
  * 만료 날짜 없음
  * 사용 안 함 없음
  * 가져오기, 키 래핑, 키 래핑 해제 작업도 수행 가능

## <a name="set-the-right-permissions-for-key-operations"></a>주요 작업에 적합한 사용 권한 설정

1. 키 자격 증명 모음에서 **액세스 정책** > **추가 정책입니다.**

   ![키 볼트의 스크린샷, 액세스 정책 및 액세스 추가 정책이 강조 표시되어 있습니다.](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. **키 사용 권한을**선택하고 **[[] []**[] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [ **Wrap** **Unwrap** **Principal** 서버 주체를 기존 보안 주체 목록에서 찾을 수 없는 경우 서버 주체를 등록해야 합니다. 데이터 암호화를 처음 설정하려고 할 때 서버 주체를 등록하라는 메시지가 표시되고 실패합니다.

   ![액세스 정책 개요](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. **저장**을 선택합니다.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>MySQL용 Azure 데이터베이스에 대한 데이터 암호화 설정

1. MySQL용 Azure 데이터베이스에서 **데이터 암호화를** 선택하여 고객 관리 키를 설정합니다.

   ![데이터 암호화가 강조 표시된 MySQL용 Azure 데이터베이스의 스크린샷](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. 키 자격 증명 모음 및 키 쌍을 선택하거나 키 식별자를 입력할 수 있습니다.

   ![데이터 암호화 옵션이 강조 표시된 MySQL용 Azure 데이터베이스의 스크린샷](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **저장**을 선택합니다.

4. 임시 파일(임시 파일 포함)이 완전히 암호화되도록 하려면 서버를 다시 시작합니다.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>복원 또는 복제 서버에 데이터 암호화 사용

MySQL용 Azure 데이터베이스가 키 볼트에 저장된 고객의 관리 키로 암호화된 후 새로 만든 서버 복사본도 암호화됩니다. 로컬 또는 지역 복원 작업을 통해 또는 복제본(로컬/지역 간) 작업을 통해 이 새 복사본을 만들 수 있습니다. 따라서 암호화된 MySQL 서버의 경우 다음 단계를 사용하여 암호화된 복원된 서버를 만들 수 있습니다.

1. 서버에서 > 복원 **개요를**선택합니다.**Restore**

   ![개요 및 복원강조 표시된 MySQL용 Azure 데이터베이스 스크린샷](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   또는 **설정** 제목 아래에서 복제 지원 서버의 경우 **복제**를 선택합니다.

   ![복제가 강조 표시된 MySQL용 Azure 데이터베이스의 스크린샷](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. 복원 작업이 완료되면 생성된 새 서버가 기본 서버의 키로 암호화됩니다. 그러나 서버의 기능 및 옵션은 비활성화되어 서버에 액세스할 수 없습니다. 이렇게 하면 새 서버의 ID가 키 자격 증명 모음에 액세스할 수 있는 권한이 아직 부여되지 않았기 때문에 데이터 조작을 방지할 수 있습니다.

   ![액세스할 수 없는 상태가 강조 표시된 MySQL용 Azure 데이터베이스의 스크린샷](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. 서버에 액세스할 수 있도록 하려면 복원된 서버의 키를 다시 유효성을 검사합니다. **데이터 암호화** > **유효성 을 다시 유효성 을 다시 확인합니다.**

   > [!NOTE]
   > 새 서버의 서비스 주체에 키 자격 증명 모음에 대한 액세스 권한을 부여해야 하므로 유효성 을 다시 검사하는 첫 번째 시도가 실패합니다. 서비스 주체를 생성하려면 다시 **유효성 검사 키를**선택하여 오류를 표시하지만 서비스 주체를 생성합니다. 그런 다음 이 문서의 앞에서 [다음 단계를](#set-the-right-permissions-for-key-operations) 참조하십시오.

   ![재검증 단계가 강조 표시된 MySQL용 Azure 데이터베이스의 스크린샷](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   새 서버에 대한 키 자격 증명 모음 액세스 권한을 부여해야 합니다.

4. 서비스 주체를 등록한 후 키를 다시 유효성을 다시 검사하고 서버가 정상적인 기능을 다시 시작합니다.

   ![복원된 기능을 보여주는 MySQL용 Azure 데이터베이스의 스크린샷](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>다음 단계

 데이터 암호화에 대한 자세한 내용은 [고객 관리 키가 있는 MySQL 데이터 암호화용 Azure Database를](concepts-data-encryption-mysql.md)참조하십시오.
