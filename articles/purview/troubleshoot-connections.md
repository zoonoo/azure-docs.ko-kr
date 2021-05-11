---
title: Azure Purview 연결 관련 문제 해결
description: 본 문서는 Azure Purview 연결 관련 문제 해결 단계를 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: c176fcafe13749ba89c04b34854f036aa5aea516
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677653"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Azure Purview 연결 관련 문제 해결

본 문서는 Azure Purview의 데이터 원본에 대한 검사를 설정하는 동안의 연결 오류 문제를 해결하는 방법을 설명합니다.

## <a name="permission-the-credential-on-the-data-source"></a>데이터 원본에 대한 자격 증명 권한

관리형 ID나 서비스 사용자를 검사에 대한 인증 메서드로 사용하고 있다면 해당 ID가 데이터 원본에 액세스할 수 있도록 하여야 합니다.

원본 유형별 구체적 지침은 다음과 같습니다.

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
- [Amazon S3](register-scan-amazon-s3.md#create-a-purview-credential-for-your-aws-bucket-scan)
## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>키 자격 증명 모음에 자격 증명을 저장하고 올바른 비밀 이름 및 버전 사용하기

또한 Azure Key Vault 인스턴스에 자격 증명을 저장하고 올바른 비밀 이름 및 버전을 사용하여야 합니다.

다음 단계를 수행하여 이를 확인합니다.

1. 키 자격 증명 모음으로 이동합니다.
1. **설정** > **비밀** 을 선택합니다.
1. 검사할 데이터 원본에 대하여 인증하기 위하여 사용하는 비밀을 선택합니다.
1. 사용하려는 버전을 선택하고 암호나 계정 키가 옳은 것인지 **비밀 값 표시** 를 클릭하여 확인합니다. 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Azure Key Vault에서 Purview 관리형 ID에 대한 권한 확인하기

Purview 관리형 ID에 Azure Key Vault에 액세스하기 위한 올바른 권한이 구성되었는지 확인합니다.

이를 확인하려면 다음 단계를 수행합니다.

1. 키 자격 증명 모음 및 **액세스 정책** 섹션으로 이동합니다.

1. Purview 관리형 ID가 *현재 액세스 정책* 섹션에서 적어도 비밀 관련 **Get** 및 **List** 권한이 있는 것으로 표시되는지 확인합니다.

   :::image type="content" source="./media/troubleshoot-connections/verify-minimum-permissions.png" alt-text="Get과 List 권한 옵션 두 가지를 모두 선택한 드롭다운을 보여 주는 이미지":::

목록에 Purview 관리형 ID가 없으면 [검사용 자격 증명 만들고 관리하기](manage-credentials.md)의 단계에 따라 해당 ID를 추가합니다. 

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
