---
author: rothja
ms.service: key-vault
ms.topic: include
ms.date: 04/21/2020
ms.author: jroth
ms.openlocfilehash: 01b3c9584f3ecddbcdcc6938f5eb469510a47a4e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85839010"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>키 트랜잭션(지역당 자격 증명 모음당 10초 내에 허용되는 최대 트랜잭션<sup>1</sup>):

|키 유형|HSM 키<br>CREATE 키|HSM 키<br>모든 다른 트랜잭션|소프트웨어 키<br>CREATE 키|소프트웨어 키<br>모든 다른 트랜잭션|
|:---|---:|---:|---:|---:|
|RSA 2,048비트|5|1,000|10|2,000|
|RSA 3,072비트|5|250|10|500|
|RSA 4,096비트|5|125|10|250|
|ECC P-256|5|1,000|10|2,000|
|ECC P-384|5|1,000|10|2,000|
|ECC P-521|5|1,000|10|2,000|
|ECC SECP256K1|5|1,000|10|2,000|

> [!NOTE]
> 위의 표에서 RSA 2,048비트 소프트웨어 키에는 10초당 2,000개 GET 트랜잭션이 허용됩니다. RSA 2,048비트 HSM 키의 경우 10초당 1,000개 GET 트랜잭션이 허용됩니다.
>
> 제한 임계값은 가중치를 부여한 값이며, 해당 합계에 적용되어 있습니다. 예를 들어 앞의 표에 나와 있는 것처럼 RSA HSM 키에 대한 GET 작업을 수행할 때 4,096 비트 키를 사용할 경우 비용이 2,048비트 키보다 8배 더 듭니다. 1000/125 = 8이기 때문입니다.
>
> Azure Key Vault 클라이언트는 `429` 제한 HTTP 상태 코드가 표시되기 전에 지정된 10초 간격으로 다음 작업 중 *하나만* 수행할 수 있습니다.
> - 2,000RSA 2,048비트 소프트웨어 키 GET 트랜잭션
> - 1,000RSA 2,048비트 HSM 키 GET 트랜잭션
> - 125RSA 4,096비트 HSM 키 GET 트랜잭션
> - 124RSA 4,096비트 HSM 키 GET 트랜잭션 및 8RSA 2,048비트 HSM 키 GET 트랜잭션

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>비밀, 관리되는 스토리지 계정 키 및 자격 증명 모음 트랜잭션:

| 트랜잭션 유형 | 지역당, 자격 증명 모음당 10초 내에 허용되는 최대 트랜잭션<sup>1</sup> |
| --- | --- |
| 모든 트랜잭션 |2,000 |

이러한 한도에 도달할 경우 제한을 처리하는 방법에 대한 내용은 [Azure Key Vault 제한 지침](../articles/key-vault/key-vault-ovw-throttling.md)을 참조하세요.

<sup>1</sup> 모든 트랜잭션 유형에 대한 구독 차원의 한도는 키 자격 증명 모음 한도의 5배입니다. 예를 들어 구독당 HSM-기타 트랜잭션은 구독당 10초 내에 5,000개 트랜잭션으로 제한됩니다.

### <a name="azure-private-link-integration"></a>Azure Private Link 통합

> [!NOTE]
> 구독 당 프라이빗 엔드포인트를 사용하도록 설정된 키 자격 증명 모듬의 수는 조정할 수 있는 제한입니다. 아래에 표시된 제한은 기본 제한입니다. 서비스에 대한 한도 증가를 요청하려면 akv-privatelink@microsoft.com에 전자 메일을 보내 주세요. 이러한 요청은 사례별 기준으로 승인합니다.

| 리소스 | 제한 |
| -------- | ----- |
| 키 자격 증명 모음당 프라이빗 엔드포인트 | 64 |
| 구독당 프라이빗 엔드포인트가 포함된 키 자격 증명 모음 | 400 |
