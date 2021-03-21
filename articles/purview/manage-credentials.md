---
title: 검색을 위한 자격 증명 만들기 및 관리
description: Azure 부서의 범위에서 자격 증명을 만들고 관리 하는 단계에 대해 알아봅니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 3802d25ebd8f21ab5b8991a66ceb6650f2f276a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461711"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Azure 부서의 범위에서 원본 인증에 대 한 자격 증명

이 문서에서는 Azure 부서의 범위에서 자격 증명을 만드는 방법을 설명 합니다. 이러한 저장 된 자격 증명을 사용 하면 저장 된 인증 정보를 신속 하 게 다시 사용 하 고 데이터 원본 검색에 적용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- Azure 키 자격 증명 모음. 새로 만드는 방법을 알아보려면 [빠른 시작: Azure Portal 사용 하 여 key Vault 만들기](../key-vault/general/quick-create-portal.md)를 참조 하세요.

## <a name="introduction"></a>소개

자격 증명은 Azure 부서의 범위에서 등록 된 데이터 원본에 인증 하는 데 사용할 수 있는 인증 정보입니다. 사용자 이름/암호가 필요한 기본 인증 등 다양 한 유형의 인증 시나리오에 대 한 자격 증명 개체를 만들 수 있습니다. 자격 증명 캡처 선택한 인증 방법에 따라 인증 하는 데 필요한 특정 정보입니다. 자격 증명 자격 증명을 만드는 동안 중요 한 인증 정보를 검색 하기 위해 기존 Azure 주요 자격 증명 모음 암호를 사용 합니다.

## <a name="use-purview-managed-identity-to-set-up-scans"></a>부서의 범위 관리 id를 사용 하 여 검색 설정

부서의 범위 관리 id를 사용 하 여 검색을 설정 하는 경우 자격 증명을 명시적으로 만들고 키 자격 증명 모음을 부서의 범위에 연결 하 여 저장소를 저장할 필요가 없습니다. 부서의 범위 관리 id를 추가 하 여 데이터 원본을 검색 하는 방법에 대 한 자세한 내용은 아래의 데이터 원본 관련 인증 섹션을 참조 하세요.

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Azure 부서의 범위 계정에 Azure 주요 자격 증명 모음 연결 만들기

자격 증명을 만들기 전에 먼저 하나 이상의 기존 Azure Key Vault 인스턴스를 Azure 부서의 범위 계정에 연결 합니다.

1. [Azure Portal](https://portal.azure.com)에서 azure 부서의 범위 계정을 선택 하 고 Azure 부서의 범위 Studio를 엽니다. Azure 부서의 범위 Studio에서 **관리 센터로** 이동한 후 **자격 증명** 으로 이동 합니다.

2. **자격 증명** 페이지에서 **관리 Key Vault 연결** 을 선택 합니다.

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Azure Key Vault 연결 관리":::

3. Key Vault 연결 관리 페이지에서 **+ 새로 만들기** 를 선택 합니다.

4. 필요한 정보를 입력 한 다음 **만들기** 를 선택 합니다.

5. 이 예에 표시 된 것 처럼 Key Vault Azure 부서의 범위 계정에 성공적으로 연결 되었는지 확인 합니다.

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="확인을 위해 Azure Key Vault 연결을 봅니다.":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>부서의 범위 관리 id에 Azure Key Vault에 대 한 액세스 권한 부여

1. Azure Key Vault로 이동 합니다.

2. **액세스 정책** 페이지를 선택 합니다.

3. **액세스 정책 추가** 를 선택합니다.

   :::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="AKV에 부서의 범위 MSI 추가":::

4. **비밀 권한** 드롭다운에서 **가져오기** 및 **목록** 사용 권한을 선택 합니다.

5. **보안 주체 선택** 에서 부서의 범위 관리 id를 선택 합니다. 부서의 범위 인스턴스 이름 **또는** 관리 id 응용 프로그램 id를 사용 하 여 부서의 범위 MSI를 검색할 수 있습니다. 현재는 복합 id (관리 id 이름 + 응용 프로그램 ID)를 지원 하지 않습니다.

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="액세스 정책 추가":::

6. **추가** 를 선택합니다.

7. **저장** 을 선택 하 여 액세스 정책을 저장 합니다.

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="액세스 정책 저장":::

## <a name="create-a-new-credential"></a>새 자격 증명 만들기

이러한 자격 증명 유형은 부서의 범위에서 지원 됩니다.

- 기본 인증: 암호를 키 자격 증명 모음에 **암호** 로 추가 합니다.
- 서비스 사용자: 키 자격 증명 모음에 비밀으로 **서비스 사용자 키** 를 추가 합니다.
- SQL 인증: 키 자격 증명 모음에 **암호를 암호로** 추가 합니다.
- 계정 키: **계정 키** 를 키 자격 증명 모음에 암호로 추가 합니다.
- 역할 ARN: Amazon S3 데이터 원본의 경우 AWS에서 **역할 ARN** 을 추가 합니다. 

자세한 내용은 [Key Vault에 암호 추가](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) 및 [부서의 범위에 대 한 새 AWS 역할 만들기](register-scan-amazon-s3.md#create-a-new-aws-role-for-purview)를 참조 하세요.

키 자격 증명 모음에 비밀을 저장 한 후:

1. Azure 부서의 범위에서 자격 증명 페이지로 이동 합니다.

2. **+ 새로** 만들기를 선택 하 여 새 자격 증명을 만듭니다.

3. 필요한 정보를 제공 합니다. 암호를 선택할 **인증 방법** 및 **Key Vault 연결** 을 선택 합니다.

4. 모든 세부 정보를 입력 한 후 **만들기** 를 선택 합니다.

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="새 자격 증명":::

5. 새 자격 증명이 목록 뷰에 표시 되 고 사용할 준비가 되었는지 확인 합니다.

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="자격 증명 보기":::

## <a name="manage-your-key-vault-connections"></a>주요 자격 증명 모음 연결 관리

1. 이름으로 Key Vault 연결 검색/찾기

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="키 자격 증명 모음 검색":::

2. 하나 이상의 Key Vault 연결 삭제

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="키 자격 증명 모음 삭제":::

## <a name="manage-your-credentials"></a>자격 증명 관리

1. 이름으로 자격 증명을 검색 하 고 찾습니다.
  
2. 기존 자격 증명을 선택 하 고 업데이트 합니다.

3. 하나 이상의 자격 증명을 삭제 합니다.

## <a name="next-steps"></a>다음 단계

[검사 규칙 집합 만들기](create-a-scan-rule-set.md)
