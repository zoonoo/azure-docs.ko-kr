---
title: Azure Database for MySQL - 데이터 암호화의 유효성을 검사하는 방법
description: Azure Database for MySQL - 고객 관리형 키를 사용하는 데이터 암호화의 암호화 유효성을 검사하는 방법을 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: 1f47b3d5679b70461eaba64b5815770162cb6fda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93241075"
---
# <a name="validating-data-encryption-for-azure-database-for-mysql"></a>Azure Database for MySQL에 대한 데이터 암호화 유효성 검사

이 문서는 Azure Database for MySQL에 대한 고객 관리형 키를 사용하는 데이터 암호화가 예상대로 작동하는지 확인하는 데 도움이 됩니다.

## <a name="check-the-encryption-status"></a>암호화 상태 확인

### <a name="from-portal"></a>포털에서

1. 고객 키가 암호화에 사용되는지 확인하려면 다음 단계를 수행합니다.

    * Azure Portal에서 **Azure Key Vault** -> **키** 로 이동합니다.
    * 서버 암호화에 사용되는 키를 선택합니다.
    * **사용** 키의 상태를 **아니요** 로 설정합니다.
  
       잠시 후에( **~15분**) Azure Database for MySQL 서버 **상태** 가 **액세스할 수 없음** 으로 전환됩니다. 서버에 대해 수행된 모든 I/O 작업이 실패하여 서버가 실제로 고객 키로 암호화되었으며 현재 키가 유효하지 않음을 확인합니다.
    
       서버를 다시 **사용 가능** 으로 만들기 위해 키의 유효성을 재검사할 수 있습니다. 
    
    * Key Vault에서 키 상태를 **예** 로 설정합니다.
    * 서버 **데이터 암호화** 에서 **키 유효성 재검사** 를 선택합니다.
    * 키 유효성 재검사에 성공하면 서버 **상태** 가 **사용 가능** 으로 변경됩니다.

2. Azure Portal에서 암호화 키가 설정되었는지 확인할 수 있는 경우 Azure Portal에서 사용된 고객 키를 사용하여 데이터가 암호화된 것입니다.

  :::image type="content" source="media/concepts-data-access-and-security-data-encryption/byok-validate.png" alt-text="액세스 정책 개요":::

### <a name="from-cli"></a>CLI에서

1. *az CLI* 명령을 사용하여 Azure Database for MySQL 서버에 사용되는 키 리소스의 유효성을 검사할 수 있습니다.

    ```azurecli-interactive
   az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    데이터 암호화가 설정되지 않은 서버에 대해 이 명령을 사용하면 빈 집합 []이 반환됩니다.

### <a name="azure-audit-reports"></a>Azure 감사 보고서

데이터 보호 표준 및 규정 요구 사항 준수에 대한 정보를 제공하는 [감사 보고서](https://servicetrust.microsoft.com)도 검토할 수 있습니다.

## <a name="next-steps"></a>다음 단계

데이터 암호화에 대한 자세한 내용은 [고객 관리형 키를 사용하는 Azure Database for MySQL 데이터 암호화](concepts-data-encryption-mysql.md)를 참조하세요.