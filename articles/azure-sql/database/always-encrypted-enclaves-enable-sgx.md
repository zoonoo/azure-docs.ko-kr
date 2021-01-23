---
title: Azure SQL Database에 대해 Intel SGX 사용
description: SGX 사용 하드웨어 생성을 선택 하 여 Azure SQL Database에서 secure enclaves를 사용 하 여 Always Encrypted에 대해 Intel SGX를 사용 하도록 설정 하는 방법을 알아봅니다.
keywords: 데이터 암호화, sql 암호화, 데이터베이스 암호화, 중요 한 데이터, Always Encrypted, secure enclaves, SGX, 증명
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: ded1406c47bb3f00c366da7a5b28319f3712f8a7
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733759"
---
# <a name="enable-intel-sgx-for-your-azure-sql-database"></a>Azure SQL Database에 대해 Intel SGX 사용 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted secure enclaves for Azure SQL Database는 현재 **공개 미리 보기로** 제공 됩니다.

Azure SQL Database의 [secure enclaves Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) 는 intel [SGX (Software Guard Extensions)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) enclaves를 사용 합니다. Intel SGX를 사용 하려면 데이터베이스에서 [Vcore 모델](service-tiers-vcore.md) 및 [DC 시리즈](service-tiers-vcore.md#dc-series) 하드웨어 생성을 사용 해야 합니다.

Intel SGX enclaves를 사용 하도록 DC 시리즈 하드웨어 생성을 구성 하는 것은 Azure SQL Database 관리자의 책임입니다. [SGX enclaves 및 증명을 구성할 때의 역할 및 책임](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation)을 참조 하세요.

> [!NOTE]
> Intel SGX는 DC 시리즈 이외의 하드웨어 세대에서 사용할 수 없습니다. 예를 들어 Intel SGX는 Gen5 하드웨어에 사용할 수 없으며 [DTU 모델](service-tiers-dtu.md)을 사용 하는 데이터베이스에 사용할 수 없습니다.

> [!IMPORTANT]
> 데이터베이스에 대 한 DC 시리즈 하드웨어 생성을 구성 하기 전에 DC 시리즈의 지역별 가용성을 확인 하 고 성능 제한 사항을 이해 하 고 있는지 확인 합니다. 자세한 내용은 [DC 시리즈](service-tiers-vcore.md#dc-series)를 참조 하세요.

특정 하드웨어 생성을 사용 하도록 새 데이터베이스 또는 기존 데이터베이스를 구성 하는 방법에 대 한 자세한 지침은 [하드웨어 생성 선택](service-tiers-vcore.md#selecting-a-hardware-generation)을 참조 하세요.
   
## <a name="next-steps"></a>다음 단계

- [Azure SQL database 서버에 대 한 Azure 증명 구성](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>참고 항목

- [자습서: Azure SQL Database의 보안 Enclave를 사용한 Always Encrypted 시작](always-encrypted-enclaves-getting-started.md)