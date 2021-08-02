---
title: Always Encrypted를 위해 Intel SGX 사용
description: SGX 지원 하드웨어 생성을 선택하여 Azure SQL Database에서 보안 enclave를 사용한 Always Encrypted를 위해 Intel SGX를 사용하도록 설정하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 1bfab8547f3c6cc010036170c082eecdb4e335ae
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111411570"
---
# <a name="enable-intel-sgx-for-always-encrypted-for-your-azure-sql-database"></a>Azure SQL Database에서 Always Encrypted를 위해 Intel SGX 사용 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database용 보안 enclave를 사용한 Always Encrypted는 현재 **공개 미리 보기** 상태입니다.

Azure SQL Database에서 [보안 enclave를 사용한 Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves)는 [Intel SGX(Intel Software Guard Extensions)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) enclave를 사용합니다. Intel SGX를 사용할 수 있으려면 데이터베이스가 [vCore 모델](service-tiers-vcore.md) 및 [DC 시리즈](service-tiers-sql-database-vcore.md#dc-series) 하드웨어 세대를 사용해야 합니다.

Intel SGX enclave를 사용하도록 DC 시리즈 하드웨어 생성을 구성하는 것은 Azure SQL Database 관리자의 책임입니다. [SGX enclave 및 증명을 구성할 때의 역할 및 책임](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation)을 참조하세요.

> [!NOTE]
> Intel SGX는 DC 시리즈 이외의 하드웨어 세대에서 사용할 수 없습니다. 예를 들어 Intel SGX는 Gen5 하드웨어에 사용할 수 없으며 [DTU 모델](service-tiers-dtu.md)을 사용하는 데이터베이스에 사용할 수 없습니다.

> [!IMPORTANT]
> 데이터베이스에 대한 DC 시리즈 하드웨어 세대를 구성하기 전에 DC 시리즈의 지역별 가용성을 확인하고 성능 제한 사항을 이해하고 있는지 확인합니다. 자세한 내용은 [DC 시리즈](service-tiers-sql-database-vcore.md#dc-series)를 참조하세요.

특정 하드웨어 세대를 사용하도록 신규 또는 기존 데이터베이스를 구성하는 방법에 대한 자세한 지침은 [하드웨어 세대 선택](service-tiers-sql-database-vcore.md#selecting-a-hardware-generation)을 참조하세요.
   
## <a name="next-steps"></a>다음 단계

- [Azure SQL 데이터베이스 서버에 대한 Azure Attestation 구성](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>참고 항목

- [자습서: Azure SQL Database의 보안 Enclave를 사용한 Always Encrypted 시작](always-encrypted-enclaves-getting-started.md)