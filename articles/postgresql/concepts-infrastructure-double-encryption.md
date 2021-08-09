---
title: 인프라 이중 암호화 - Azure Database for PostgreSQL
description: 인프라 이중 암호화를 사용하여 서비스 관리형 키로 두 번째 암호화 계층을 추가하는 방법에 대해 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: 83635b732318a4ada76d1d71c1ce419cae8b35e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98630148"
---
# <a name="azure-database-for-postgresql-infrastructure-double-encryption"></a>Azure Database for PostgreSQL 인프라 이중 암호화

Azure Database for PostgreSQL은 Microsoft의 관리형 키를 사용하여 데이터에 대해 [미사용 데이터의 스토리지 암호화](concepts-security.md#at-rest)를 사용합니다. 백업을 비롯한 데이터는 디스크에서 암호화되며 이 암호화는 항상 사용되고 사용하지 않도록 설정할 수 없습니다. 이 암호화는 Azure 스토리지 암호화를 위해 FIPS 140-2의 유효성 검사된 암호화 모듈과 AES 256비트 암호화를 사용합니다.

인프라 이중 암호화는 서비스 관리형 키를 사용하여 두 번째 암호화 계층을 추가합니다. FIPS 140-2의 유효성 검사된 암호화 모듈을 사용하지만 암호화 알고리즘이 다릅니다. 이는 미사용 데이터에 대한 추가 보호 계층을 제공합니다. 인프라 이중 암호화에 사용되는 키는 Azure Database for PostgreSQL 서비스를 통해서도 관리합니다. 추가 암호화 계층이 성능에 영향을 줄 수 있으므로 인프라 이중 암호화는 기본적으로 사용하도록 설정되어 있지 않습니다.

> [!NOTE]
> 이 기능은 Azure Database for PostgreSQL의 “범용” 및 “메모리 최적화” 가격 책정 계층에만 지원됩니다.

인프라 계층 암호화는 스토리지 디바이스나 네트워크 회선에 가장 가까운 계층에서 구현되는 이점이 있습니다. Azure Database for PostgreSQL은 서비스 관리형 키를 사용하여 두 가지 암호화 계층을 구현합니다. 여전히 서비스 계층에서 기술적으로는 아니지만 미사용 데이터를 저장하는 하드웨어와 매우 가깝습니다. 프로비저닝된 PostgreSQL 서버에서 [고객 관리형 키](concepts-data-encryption-postgresql.md)를 사용하여 미사용 데이터 암호화를 선택적으로 사용하도록 설정할 수 있습니다.  

인프라 계층의 구현에서도 다양한 키를 지원합니다. 인프라는 머신 및 네트워크의 여러 클러스터를 인식해야 합니다. 따라서 인프라 공격과 다양한 하드웨어 및 네트워크 오류의 폭발 반경을 최소화하기 위해 다양한 키를 사용합니다. 

> [!NOTE]
> 인프라 이중 암호화를 사용하면 추가 암호화 프로세스로 인해 Azure Database for PostgreSQL 서버의 성능에 영향을 줄 수 있습니다.

## <a name="benefits"></a>이점

Azure Database for PostgreSQL에 인프라 이중 암호화를 사용하면 다음과 같은 이점이 있습니다.

1. **암호화 구현의 추가 다양성** - 하드웨어 기반 암호화로의 계획된 이동은 소프트웨어 기반 구현 외에 하드웨어 기반 구현을 제공하여 구현을 보다 다양화합니다.
2. **구현 오류** - 인프라 계층에서 두 가지 암호화 계층은 일반 텍스트 데이터를 노출하는 상위 계층의 캐싱 또는 메모리 관리에서 발생하는 모든 오류를 방지합니다. 또한 두 계층은 일반적으로 암호화 구현 시 오류를 방지합니다.

해당 조합은 암호화를 공격하는 데 사용되는 일반적인 위협과 약점으로부터 강력한 보호를 제공합니다.

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>인프라 이중 암호화가 지원되는 시나리오

Azure Database for PostgreSQL에서 제공하는 암호화 기능은 함께 사용할 수 있습니다. 다음은 사용할 수 있는 다양한 시나리오에 대한 요약입니다.

|  ##   | 기본 암호화 | 인프라 이중 암호화 | 고객 관리형 키를 사용한 데이터 암호화  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *예*              | *아니요*                             | *아니요*                                         |
| 2     | *예*              | *예*                            | *아니요*                                         |
| 3     | *예*              | *아니요*                             | *예*                                        |
| 4     | *예*              | *예*                            | *예*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - 시나리오 2 및 4는 추가 인프라 암호화 계층으로 인해 Azure Database for PostgreSQL 서버의 성능에 영향을 줄 수 있습니다.
> - Azure Database for PostgreSQL에 대한 인프라 이중 암호화 구성은 서버를 만드는 동안에만 허용됩니다. 서버가 프로비저닝되면 스토리지 암호화를 변경할 수 없습니다. 그러나 인프라 이중 암호화를 사용하거나 사용하지 않고 생성된 서버에 대해서는 고객 관리형 키를 사용하여 데이터 암호화를 계속 사용할 수 있습니다.

## <a name="limitations"></a>제한 사항

Azure Database for PostgreSQL의 경우 서비스 관리형 키를 사용하는 인프라 이중 암호화 지원에는 다음과 같은 제한 사항이 있습니다.

* 이 기능에 대한 지원은 **범용** 및 **메모리 최적화** 가격 책정 계층으로 제한됩니다.
* 이 기능은 최대 16TB의 스토리지를 지원하는 지역 및 서버에서만 지원됩니다. 최대 16TB의 스토리지를 지원하는 Azure 지역 목록은 [스토리지 설명서](concepts-pricing-tiers.md#storage)를 참조하세요.

    > [!NOTE]
    > - 위에 나열된 지역에 생성된 모든 **새** PostgreSQL 서버도 고객 관리자 키로 데이터 암호화를 지원합니다. 이 경우 PITR(특정 시점 복원) 또는 읽기 복제본을 통해 만든 서버는 “새 항목”으로 한정되지 않습니다.
    > - 프로비저닝된 서버에서 최대 16TB를 지원하는지 확인하려면 포털의 가격 책정 계층 블레이드로 이동하여 스토리지 슬라이더를 최대 16TB까지 이동할 수 있는지 확인합니다. 슬라이더를 최대 4TB까지만 이동할 수 있는 경우 서버에서 고객 관리형 키를 사용하여 암호화를 지원하지 않을 수 있습니다. 그러나 데이터는 서비스 관리형 키를 사용하여 항상 암호화됩니다. 질문이 있는 경우 AskAzureDBforPostgreSQL@service.microsoft.com에 문의하세요.

## <a name="next-steps"></a>다음 단계

[Azure Database for PostgreSQL에 인프라 이중 암호화를 설정](howto-double-encryption.md)하는 방법을 알아봅니다.
