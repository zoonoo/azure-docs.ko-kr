---
title: Azure Database for MySQL 데이터 암호화의 유효성 검사를 확인 하는 방법
description: 고객 관리 키를 사용 하 여 Azure Database for MySQL 데이터 암호화의 암호화를 확인 하는 방법을 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: e7b747bd2babeeccb210bb30c3eb28f22d4befe7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905792"
---
# <a name="validating-data-encryption-for-azure-database-for-mysql"></a>Azure Database for MySQL에 대 한 데이터 암호화 유효성 검사

이 문서에서는 Azure Database for MySQL에 대 한 고객 관리 키를 사용 하는 데이터 암호화가 예상 대로 작동 하는지 확인 하는 데 도움이 됩니다.

## <a name="check-the-encryption-status"></a>암호화 상태 확인

### <a name="from-portal"></a>포털에서

1. 고객의 키가 암호화에 사용 되는지 확인 하려면 다음 단계를 수행 합니다.

    * Azure Portal에서 **Azure Key Vault**  ->  **키** 로 이동 합니다.
    * 서버 암호화에 사용 되는 키를 선택 합니다.
    * 키 **사용** 의 상태를 **아니요**로 설정 합니다.
  
       잠시 후 (**~ 15 분**) Azure Database for MySQL 서버 **상태** 에 **액세스할**수 없습니다. 서버에 대해 수행 되는 모든 i/o 작업은 서버가 고객 키를 사용 하 여 실제로 암호화 되어 있고 키가 현재 유효 하지 않은지 확인 하는 작업이 실패 합니다.
    
       서버를에 **사용할 수** 있도록 하려면 키의 유효성을 다시 검사할 수 있습니다. 
    
    * Key Vault의 키 상태를 **예**로 설정 합니다.
    * 서버 **데이터 암호화**에서 **키 유효성**다시 검사를 선택 합니다.
    * 키 유효성 재검사가 성공적으로 완료 되 면 서버 **상태가** **사용 가능**으로 변경 됩니다.

2. Azure Portal에서 암호화 키가 설정 되어 있는지 확인할 수 있는 경우 Azure Portal에서 사용 되는 고객 키를 사용 하 여 데이터가 암호화 됩니다.

  :::image type="content" source="media/concepts-data-access-and-security-data-encryption/byok-validate.png" alt-text="액세스 정책 개요":::

### <a name="from-cli"></a>CLI에서

1. *AZ CLI* 명령을 사용 하 여 Azure Database for MySQL 서버에 사용 되는 키 리소스의 유효성을 검사할 수 있습니다.

    ```azurecli-interactive
   az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    데이터 암호화가 설정 되지 않은 서버의 경우이 명령을 사용 하면 빈 집합 []이 반환 됩니다.

### <a name="azure-audit-reports"></a>Azure 감사 보고서

데이터 보호 표준 및 규정 요구 사항을 준수 하는 방법에 대 한 정보를 제공 하는 [감사 보고서](https://servicetrust.microsoft.com) 도 검토할 수 있습니다.

## <a name="next-steps"></a>다음 단계

데이터 암호화에 대 한 자세한 내용은 [고객 관리 키를 사용 하 여 데이터 암호화 Azure Database for MySQL](concepts-data-encryption-mysql.md)를 참조 하세요.