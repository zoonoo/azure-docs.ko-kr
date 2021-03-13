---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 03/09/2021
ms.author: ambapat
ms.openlocfilehash: d934d40cad5f4eec929cfd273b6e30ea291e48d5
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010962"
---
Azure Key Vault 리소스 공급자는 자격 증명 모음과 관리형 HSM이라는 두 가지 리소스 유형을 지원합니다. 다음 두 섹션에서는 이들 각각에 대한 서비스 제한을 설명합니다.

### <a name="resource-type-vault"></a>리소스 종류: 자격 증명 모음

이 섹션에서는 리소스 종류 `vaults`에 대한 서비스 제한에 대해 설명합니다.

#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>키 트랜잭션(지역당 자격 증명 모음당 10초 내에 허용되는 최대 트랜잭션<sup>1</sup>):

|키 유형|HSM 키<br>CREATE 키|HSM 키<br>모든 다른 트랜잭션|소프트웨어 키<br>CREATE 키|소프트웨어 키<br>모든 다른 트랜잭션|
|:---|---:|---:|---:|---:|
|RSA 2,048비트|5|1,000|10|2,000|
|RSA 3,072비트|5|250|10|500|
|RSA 4,096비트|5|125|10|250|
|ECC P-256|5|1,000|10|2,000|
|ECC P-384|5|1,000|10|2,000|
|ECC P-521|5|1,000|10|2,000|
|ECC SECP256K1|5|1,000|10|2,000|
||||||

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

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>비밀, 관리되는 스토리지 계정 키 및 자격 증명 모음 트랜잭션:

| 트랜잭션 유형 | 지역당, 자격 증명 모음당 10초 내에 허용되는 최대 트랜잭션<sup>1</sup> |
| --- | --- |
| 모든 트랜잭션 |2,000 |

이러한 한도에 도달할 경우 제한을 처리하는 방법에 대한 내용은 [Azure Key Vault 제한 지침](../articles/key-vault/general/overview-throttling.md)을 참조하세요.

<sup>1</sup> 모든 트랜잭션 유형에 대한 구독 차원의 한도는 키 자격 증명 모음 한도의 5배입니다. 예를 들어 구독당 HSM-기타 트랜잭션은 구독당 10초 내에 5,000개 트랜잭션으로 제한됩니다.

#### <a name="azure-private-link-integration"></a>Azure Private Link 통합

> [!NOTE]
> 구독 당 프라이빗 엔드포인트를 사용하도록 설정된 키 자격 증명 모듬의 수는 조정할 수 있는 제한입니다. 아래에 표시된 제한은 기본 제한입니다. 서비스에 대한 한도 증가를 요청하려면 akv-privatelink@microsoft.com에 전자 메일을 보내 주세요. 이러한 요청은 사례별 기준으로 승인합니다.

| 리소스 | 제한 |
| -------- | -----:|
| 키 자격 증명 모음당 프라이빗 엔드포인트 | 64 |
| 구독당 프라이빗 엔드포인트가 포함된 키 자격 증명 모음 | 400 |

### <a name="resource-type-managed-hsm-preview"></a>리소스 종류: 관리형 HSM(미리 보기)

이 섹션에서는 리소스 종류 `managed HSM`에 대한 서비스 제한에 대해 설명합니다.

#### <a name="object-limits"></a>개체 제한

|항목|제한|
|----|------:|
지역별 구독당 HSM 인스턴스 수|1(미리 보기 중)
HSM 풀당 키 수|5,000
키당 버전 수|100
HSM당 사용자 지정 역할 정의 수|50
HSM 범위에서 역할 할당 수|50
각 개별 키 범위의 역할 할당 수|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>관리 작업에 대한 트랜잭션 제한(HSM 인스턴스별 초당 작업 수)
|작업 |초당 SQL 작업 수|
|----|------:|
모든 RBAC 작업<br/>(역할 정의 및 역할 할당에 대한 모든 CRUD 작업 포함)|5
전체 HSM 백업/복원<br/>(HSM 인스턴스당 하나의 동시 백업 또는 복원 작업만 지원됨)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>암호화 작업에 대한 트랜잭션 제한(HSM 인스턴스별 초당 작업 수)

- 각 관리형 HSM 인스턴스는 3개의 부하 분산된 HSM 파티션을 구성합니다. 처리량 제한은 각 파티션에 할당된 기본 하드웨어 용량의 함수입니다. 아래 표는 사용 가능한 파티션이 하나 이상인 최대 처리량을 보여줍니다. 3개의 파티션을 모두 사용할 수 있는 경우 실제 처리량은 최대 3배까지 높아질 수 있습니다.
- 명시된 처리량 제한은 최대 처리량을 달성하기 위해 하나의 단일 키를 사용한다고 가정합니다. 예를 들어 단일 RSA-2048 키를 사용하는 경우 최대 처리량은 1100개의 서명 작업이 됩니다. 초당 1개의 트랜잭션과 함께 1100개의 서로 다른 키를 사용하는 경우 동일한 처리량을 달성할 수 없습니다.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>RSA 키 작업(HSM 인스턴스별 초당 작업 수)

|작업|2048비트|3072비트|4096비트|
|--|--:|--:|--:|
키 만들기|1| 1| 1
키 삭제(일시 삭제)|10|10|10 
키 제거|10|10|10 
키 백업|10|10|10 
키 복원|10|10|10 
키 정보 가져오기|1100|1100|1100
Encrypt|10000|10000|6000
암호 해독|1100|360|160
래핑|10000|10000|6000
래핑 취소|1100|360|160
Sign|1100|360|160
확인|10000|10000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>EC 키 작업(HSM 인스턴스별 초당 작업 수)

이 표는 각 곡선 유형에 대한 초당 작업 수를 설명합니다.

|작업|P-256|P-256K|P-384|P-521|
|---|---:|---:|---:|---:|
키 만들기| 1| 1| 1| 1
키 삭제(일시 삭제)|10|10|10|10
키 제거|10|10|10|10
키 백업|10|10|10|10
키 복원|10|10|10|10
키 정보 가져오기|1100|1100|1100|1100
Sign|260|260|165|56
확인|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>AES 키 작업(HSM 인스턴스별 초당 작업 수)
- 암호화 및 암호 해독 작업은 4KB 패킷 크기를 가정합니다.
- 암호화/암호 해독에 대한 처리량 제한은 AES-CBC 및 AES-GCM 알고리즘에 적용됩니다.
- 래핑/래핑 해제에 대한 처리량 제한은 AES-KW 알고리즘에 적용됩니다.

|작업|128비트|192비트|256비트|
|--|--:|--:|--:|
키 만들기|1| 1| 1
키 삭제(일시 삭제)|10|10|10
키 제거|10|10|10
키 백업|10|10|10
키 복원|10|10|10
키 정보 가져오기|1100|1100|1100
Encrypt|8000|8000 |8000 
암호 해독|8000|8000|8000
래핑|9000|9000|9000
래핑 취소|9000|9000|9000

