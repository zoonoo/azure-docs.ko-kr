---
title: Azure 부서의 범위에서 연결 문제 해결
description: 이 문서에서는 Azure 부서의 범위에서 연결 문제를 해결 하는 단계를 설명 합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 8be5def6d4780fd8b2ab65c5c9e1104a869f6d5a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391595"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Azure 부서의 범위에서 연결 문제 해결

이 문서에서는 Azure 부서의 범위에서 데이터 원본에 대 한 검색을 설정 하는 동안 연결 오류 문제를 해결 하는 방법을 설명 합니다.

## <a name="permission-the-credential-on-the-data-source"></a>데이터 원본에 대 한 자격 증명 권한

검색에 대 한 인증 방법으로 관리 되는 id 또는 서비스 주체를 사용 하는 경우 이러한 id가 데이터 원본에 액세스할 수 있도록 허용 해야 합니다.

각 원본 유형에 대 한 구체적인 지침은 다음과 같습니다.

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#setting-up-authentication-for-a-scan)
- [Azure Data Explorer](register-scan-azure-data-explorer.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
- [SQL Server](register-scan-on-premises-sql-server.md#setting-up-authentication-for-a-scan)
- [Power BI](register-scan-power-bi-tenant.md)

## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>키 자격 증명 모음에 자격 증명을 저장 하 고 올바른 비밀 이름 및 버전 사용

또한 Azure Key Vault 인스턴스에 자격 증명을 저장 하 고 올바른 비밀 이름 및 버전을 사용 해야 합니다.

다음 단계를 수행 하 여이를 확인 합니다.

1. Key Vault로 이동 합니다.
1. **설정** > **비밀** 을 선택합니다.
1. 검색을 위해 데이터 원본에 대해 인증 하는 데 사용 하는 암호를 선택 합니다.
1. 사용 하려는 버전을 선택 하 고 **비밀 값 표시** 를 클릭 하 여 암호나 계정 키가 올바른지 확인 합니다. 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Azure Key Vault에서 부서의 범위 관리 id에 대 한 사용 권한을 확인 합니다.

Azure Key Vault에 액세스 하도록 부서의 범위 관리 id에 대해 올바른 권한이 구성 되었는지 확인 합니다.

이를 확인 하려면 다음 단계를 수행 합니다.

1. 키 자격 증명 모음 및 **액세스 정책** 섹션으로 이동 합니다.
1. 부서의 범위 관리 id가 *현재 액세스 정책* 섹션 아래에 표시 되는 암호에 대 한 **get** 및 **list** 권한이 있는 다음 이상 인지 확인 합니다.

부서의 범위 관리 id가 표시 되지 않으면 [검색에 대 한 자격 증명 만들기 및 관리](manage-credentials.md) 의 단계에 따라 추가 합니다. 

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
