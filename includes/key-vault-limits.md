---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2d16febd4676ca7ba763eb7bc6dcecda4608ebb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74224493"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>주요 트랜잭션(리전당 볼트당 최대 트랜잭션이 10초 로<sup>허용됨) 1:</sup>

|키 유형|HSM 키<br>키 만들기|HSM 키<br>모든 다른 트랜잭션|소프트웨어 키<br>키 만들기|소프트웨어 키<br>모든 다른 트랜잭션|
|:---|---:|---:|---:|---:|
|RSA 2,048비트|5|1,000|10|2,000|
|RSA 3,072 비트|5|250|10|500|
|RSA 4,096 비트|5|125|10|250|
|ECC P-256|5|1,000|10|2,000|
|ECC P-384|5|1,000|10|2,000|
|ECC P-521|5|1,000|10|2,000|
|ECC SECP256K1|5|1,000|10|2,000|

> [!NOTE]
> 이전 표에서는 RSA 2,048비트 소프트웨어 키의 경우 10초당 2,000GET 트랜잭션이 허용됩니다. RSA 2,048비트 HSM 키의 경우 10초당 1,000GET 트랜잭션이 허용됩니다.
>
> 제한 임계값은 가중치가 적용되고 적용은 합계에 있습니다. 예를 들어 이전 표와 같이 RSA HSM 키에서 GET 작업을 수행할 때 2,048비트 키에 비해 4,096비트 키를 사용하는 데 8배 더 비쌉입니다. 1,000/125 = 8이기 때문입니다.
>
> 지정된 10초 간격으로 Azure Key Vault 클라이언트는 `429` 제한 HTTP 상태 코드가 발생하기 전에 다음 작업 중 *하나만* 수행할 수 있습니다.
> - 2,000RSA 2,048비트 소프트웨어 키 GET 트랜잭션
> - 1,000 RSA 2,048비트 HSM 키 GET 트랜잭션
> - 125 RSA 4,096비트 HSM 키 GET 트랜잭션
> - 124 RSA 4,096비트 HSM 키 GET 트랜잭션 및 8개의 RSA 2,048비트 HSM 키 GET 트랜잭션

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>비밀, 관리 저장소 계정 키 및 자격 증명 모음 트랜잭션:
| 트랜잭션 유형 | <sup>리전1당</sup> 볼트당 10초 만에 허용되는 최대 트랜잭션 |
| --- | --- |
| 모든 트랜잭션 |2,000 |

이러한 제한을 초과하는 경우 제한을 처리하는 방법에 대한 자세한 내용은 [Azure Key Vault 제한 지침을](../articles/key-vault/key-vault-ovw-throttling.md)참조하십시오.

<sup>1</sup> 모든 트랜잭션 유형에 대한 구독 전체 제한은 키 볼트 한도당 5회입니다. 예를 들어 구독당 HSM 기타 트랜잭션은 구독당 10초 내에 5,000개의 트랜잭션으로 제한됩니다.
