---
title: Azure SQL Database에서 Intel SGX enclaves 및 증명 계획
description: Azure SQL Database에서 secure enclaves를 사용 하 여 Always Encrypted 배포를 계획 합니다.
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
ms.openlocfilehash: 4448ce051b0c9e73865e8057cc4f224c9cbeb571
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732747"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>Azure SQL Database에서 Intel SGX enclaves 및 증명 계획

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted secure enclaves for Azure SQL Database는 현재 **공개 미리 보기로** 제공 됩니다.

Azure SQL Database의 [secure enclaves Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) 는 intel [SGX (Software Guard Extensions)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) enclaves를 사용 하 고 [Microsoft Azure 증명이](/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation)필요 합니다.

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>Azure SQL Database에서 Intel SGX 계획

Intel SGX는 하드웨어 기반의 신뢰할 수 있는 실행 환경 기술입니다. Intel SGX는 [Vcore 모델](service-tiers-vcore.md) 및 [DC 시리즈](service-tiers-vcore.md?#dc-series) 하드웨어 생성을 사용 하는 데이터베이스에 사용할 수 있습니다. 따라서 데이터베이스에서 보안 enclaves을 사용 하 여 Always Encrypted를 사용 하려면 데이터베이스를 만들 때 DC 시리즈 하드웨어 생성을 선택 하거나 DC 시리즈 하드웨어 생성을 사용 하도록 기존 데이터베이스를 업데이트 해야 합니다.

> [!NOTE]
> Intel SGX는 DC 시리즈 이외의 하드웨어 세대에서 사용할 수 없습니다. 예를 들어 Intel SGX는 Gen5 하드웨어에 사용할 수 없으며 [DTU 모델](service-tiers-dtu.md)을 사용 하는 데이터베이스에 사용할 수 없습니다.

> [!IMPORTANT]
> 데이터베이스에 대 한 DC 시리즈 하드웨어 생성을 구성 하기 전에 DC 시리즈의 지역별 가용성을 확인 하 고 성능 제한 사항을 이해 하 고 있는지 확인 합니다. 자세한 내용은 [DC 시리즈](service-tiers-vcore.md#dc-series)를 참조 하세요.

## <a name="plan-for-attestation-in-azure-sql-database"></a>Azure SQL Database에서 증명 계획

[Microsoft Azure 증명](../../attestation/overview.md) (미리 보기)은 DC 시리즈 하드웨어 생성을 사용 하 여 Azure SQL 데이터베이스에서 Intel SGX enclaves를 포함 하 여 TEEs (증명 Trusted Execution environment)를 위한 솔루션입니다.

Azure SQL Database에서 증명 Intel SGX enclaves에 대해 Azure 증명을 사용 하려면 다음을 수행 해야 합니다.

1. 증명 [공급자](../../attestation/basic-concepts.md#attestation-provider) 를 만들고 증명 정책을 사용 하 여 구성 합니다. 

2. Azure SQL 논리 서버에 생성 된 증명 공급자에 대 한 액세스 권한을 부여 합니다.

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>SGX enclaves 및 증명을 구성할 때의 역할 및 책임

Azure SQL Database에서 Always Encrypted에 대 한 Intel SGX enclaves 및 증명을 지원 하도록 환경을 구성 하는 작업에는 Microsoft Azure 증명, Azure SQL Database, 그리고 enclave 증명을 트리거하는 응용 프로그램 등 다양 한 유형의 구성 요소를 설정 해야 합니다. 각 형식의 구성 요소는 다음과 같은 고유한 역할 중 하나를 수행하는 사용자가 구성합니다.

- 증명 관리자-증명 공급자를 Microsoft Azure 증명으로 만들고, 증명 정책을 만든 다음, Azure SQL 논리 서버에 증명 공급자에 대 한 액세스 권한을 부여 하 고, 정책을 가리키는 증명 URL을 응용 프로그램 관리자에 게 공유 합니다.
- Azure SQL Database 관리자-DC 시리즈 하드웨어 생성을 선택 하 여 데이터베이스에서 SGX enclaves를 사용 하도록 설정 하 고 증명 공급자에 액세스 해야 하는 Azure SQL 논리 서버의 id를 증명 관리자에 게 제공 합니다.
- 응용 프로그램 관리자-증명 관리자 로부터 얻은 증명 URL을 사용 하 여 응용 프로그램을 구성 합니다.

프로덕션 환경(실제 중요한 데이터 처리)에서 조직은 증명을 구성할 때 각 고유한 역할을 서로 다른 사용자가 수행하는 역할 분리를 준수해야 합니다. 특히 조직에서 Always Encrypted를 배포 하는 것이 목표는 Azure SQL Database 관리자가 중요 한 데이터에 액세스할 수 없도록 하 여 공격 노출 영역을 줄이는 것입니다 Azure SQL Database 관리자는 증명 정책을 제어 하지 않아야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure SQL database에 대해 Intel SGX 사용](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>참고 항목

- [자습서: Azure SQL Database의 보안 Enclave를 사용한 Always Encrypted 시작](always-encrypted-enclaves-getting-started.md)