---
title: 검사를 위한 자격 증명 생성 및 관리
description: Azure Purview에서 자격 증명을 만들고 관리하는 단계에 대해 알아봅니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 3802d25ebd8f21ab5b8991a66ceb6650f2f276a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461711"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Azure Purview에서 원본 인증을 위한 자격 증명

이 문서에서는 Azure Purview에서 자격 증명을 생성하는 방법에 대해 설명합니다. 저장된 자격 증명을 사용하면 데이터 원본 검사에 저장된 인증 정보를 빠르게 재사용하고 적용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 키 자격 증명 모음. 만드는 방법을 배우려면 [빠른 시작: Azure Portal을 사용하여 키 자격 증명 모음 만들기](../key-vault/general/quick-create-portal.md)를 참조하세요.

## <a name="introduction"></a>소개

자격 증명은 Azure Purview가 등록된 데이터 원본에 인증하는 데 사용할 수 있는 인증 정보입니다. 다양한 유형의 인증 시나리오(예: 사용자 이름/암호가 필요한 기본 인증)에 대해 자격 증명 개체를 만들 수 있습니다. 자격 증명은 선택한 인증 방법 유형에 따라 인증에 필요한 특정 정보를 캡처합니다. 자격 증명 생성 프로세스 중에 자격 증명은 중요한 인증 정보를 검색하기 위해서 기존 Azure Key Vaults 비밀을 사용합니다.

## <a name="use-purview-managed-identity-to-set-up-scans"></a>Purview 관리 ID를 사용하여 검사 설정하기

Purview 관리 ID를 사용하여 검색을 설정하는 경우, 명시적으로 자격 증명을 만들고 키 자격 증명 모음을 Purview에 연결하여 저장할 필요가 없습니다. 데이터 원본 검색에 액세스할 수 있도록 Purview 관리 ID를 추가하는 방법에 대한 자세한 지침은 아래의 데이터 원본별 인증 섹션을 참조하세요.

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Azure Purview 계정에 Azure Key Vaults 연결하기

자격 증명을 만들려면 먼저 하나 이상의 기존 Azure Key Vault 인스턴스를 Azure Purview 계정에 연결하세요.

1. [Azure Portal](https://portal.azure.com)에서 Azure Purview 계정을 선택하고 Azure Purview Studio를 엽니다. Azure Purview Studio에서 **관리 센터** 로 이동한 후 **자격 증명** 으로 이동합니다.

2. **자격 증명 페이지** 에서 **Key Vault 연결 관리하기** 를 선택합니다.

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Azure Key Vault 연결 관리":::

3. Key Vault 연결 관리하기 페이지에서 **+ 새로 만들기** 를 선택합니다.

4. 필요한 정보를 입력한 다음 **만들기** 를 선택합니다.

5. 다음 예시와 같이 Key Vault가 Azure Purview 계정에 성공적으로 연결되었는지 확인합니다.

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="확인을 위해 Azure Key Vault 연결을 봅니다.":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Azure Key Vault에 Purview 관리 ID 액세스 권한을 부여

1. Azure Key Vault로 이동합니다.

2. **액세스 정책** 페이지를 선택합니다.

3. **액세스 정책 추가** 를 선택합니다.

   :::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="AKV에 Purview MSI를 추가":::

4. **비밀 권한** 드롭다운에서 **가져오기** 및 **목록** 사용 권한을 선택합니다.

5. **보안 주체 선택** 에서 Purview 관리 ID를 선택합니다. Purview 인스턴스 이름 **또는** 관리 ID 애플리케이션 ID를 사용하여 Purview MSI를 검색할 수 있습니다. 현재는 복합 ID(관리 ID 이름 + 애플리케이션 ID)를 지원하지 않습니다.

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="액세스 정책 추가":::

6. **추가** 를 선택합니다.

7. **저장** 을 선택하여 액세스 정책을 저장합니다.

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="액세스 정책 저장":::

## <a name="create-a-new-credential"></a>새 자격 증명 만들기

다음 자격 증명 유형은 Purview에서 지원됩니다.

- 기본 인증: 키 자격 증명 모음에서 **암호** 를 비밀로 추가합니다.
- 서비스 주체: 키 자격 증명 모음에 **서비스 주체 키** 를 비밀로 추가합니다.
- SQL 인증: 키 자격 증명 모음에 **암호** 를 비밀로 추가합니다.
- 계정 키: 키 자격 증명 모음에 **계정 키** 를 비밀로 추가합니다.
- 역할 ARN: Amazon S3 데이터 원본의 경우 AWS에 **역할 ARN** 을 추가합니다. 

자세한 내용은 [Key Vault에 비밀 추가](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) 및 [ Purview에 대한 새 AWS 역할 만들기](register-scan-amazon-s3.md#create-a-new-aws-role-for-purview)를 참조하세요.

키 자격 증명 모음에 비밀을 저장한 후에 다음을 수행합니다.

1. Azure Purview에서 자격 증명 페이지로 이동합니다.

2. **+ 새로 만들기** 를 선택하여 새 자격 증명을 만듭니다.

3. 필요한 정보를 입력합니다. **인증 방법** 및 비밀을 선택할 **Key Vault 연결** 을 선택합니다.

4. 모든 세부 정보를 입력한 후 **만들기** 를 선택합니다.

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="새 자격 증명":::

5. 새 자격 증명이 목록 보기에 나타나고 사용할 준비가 되었는지 확인합니다.

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="자격 증명 보기":::

## <a name="manage-your-key-vault-connections"></a>키 자격 증명 모음 연결 관리

1. 이름으로 Key Vault 연결 검색/찾기

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="키 자격 증명 모음 검색":::

2. 하나 이상의 Key Vault 연결 삭제

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="키 자격 증명 모음 삭제":::

## <a name="manage-your-credentials"></a>자격 증명 관리

1. 이름으로 자격 증명 검색/찾기.
  
2. 기존 자격 증명을 선택하고 업데이트합니다.

3. 하나 이상의 자격 증명을 삭제합니다.

## <a name="next-steps"></a>다음 단계

[검사 규칙 집합 만들기](create-a-scan-rule-set.md)
