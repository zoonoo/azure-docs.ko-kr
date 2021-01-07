---
title: 검색을 위한 자격 증명 만들기 및 관리
description: 이 문서에서는 Azure 부서의 범위에서 자격 증명을 만들고 관리 하는 단계를 설명 합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 4c964f3661e120026189a75d331e6db975b41c70
ms.sourcegitcommit: 90caa05809d85382c5a50a6804b9a4d8b39ee31e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97756078"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Azure 부서의 범위에서 원본 인증에 대 한 자격 증명

이 문서에서는 Azure 부서의 범위에서 자격 증명을 만들어 저장 된 인증 정보를 데이터 원본 검색에 신속 하 게 다시 사용 하 고 적용 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 키 자격 증명 모음. 새로 만드는 방법을 알아보려면 [빠른 시작: Azure Portal 사용 하 여 key Vault 만들기](../key-vault/general/quick-create-portal.md)를 참조 하세요.

## <a name="introduction"></a>소개
자격 증명은 Azure 부서의 범위에서 등록 된 데이터 원본에 인증 하는 데 사용할 수 있는 인증 정보입니다. 자격 증명 개체는 다양 한 유형의 인증 시나리오 (예: 사용자 이름/암호를 요구 하는 기본 인증)에 대해 만들 수 있으며 선택한 인증 방법 유형에 따라 필요한 특정 정보를 캡처합니다. 자격 증명 자격 증명을 만드는 동안 중요 한 인증 정보를 검색 하기 위해 기존 Azure 주요 자격 증명 모음 암호를 사용 합니다.

## <a name="using-purview-managed-identity-to-set-up-scans"></a>부서의 범위 관리 id를 사용 하 여 검색 설정
부서의 범위 관리 id를 사용 하 여 검색을 설정 하는 경우 자격 증명을 명시적으로 만들고 키 자격 증명 모음을 부서의 범위에 연결 하 여 저장소를 저장할 필요가 없습니다. 부서의 범위 관리 id를 추가 하 여 데이터 원본을 검색 하는 방법에 대 한 자세한 내용은 아래의 데이터 원본 관련 인증 섹션을 참조 하세요.

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Azure 부서의 범위 계정에 Azure 주요 자격 증명 모음 연결 만들기

자격 증명을 만들기 전에 먼저 하나 이상의 기존 Azure Key Vault 인스턴스를 Azure 부서의 범위 계정에 연결 해야 합니다.

1. Azure 부서의 범위 탐색 메뉴에서 관리 센터로 이동한 후 자격 증명으로 이동 합니다.

2. 자격 증명 명령 모음에서 Key Vault 연결 관리를 선택 합니다.

    :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="AKV 연결 관리":::

3. Key Vault 연결 관리 패널에서 + 새로 만들기를 선택 합니다. 

4. 필요한 정보를 입력 한 다음 만들기를 선택 합니다.

5. Key Vault Azure 부서의 범위 계정에 성공적으로 연결 되었는지 확인 합니다.

    :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="AKV 연결 보기":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>부서의 범위 관리 id에 Azure Key Vault에 대 한 액세스 권한 부여

키 자격 증명 모음 (> 액세스 정책-액세스 정책 추가 >으로 이동 합니다. 비밀 권한 드롭다운에서 Get 권한을 부여 하 고 부서의 범위 MSI가 될 보안 주체를 선택 합니다. 

:::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="AKV에 부서의 범위 MSI 추가":::


:::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="액세스 정책 추가":::


:::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="액세스 정책 저장":::

## <a name="create-a-new-credential"></a>새 자격 증명 만들기

현재 부서의 범위에서 지원 되는 자격 증명 유형:
* 기본 인증: **암호** 를 키 자격 증명 모음에 비밀으로 추가 합니다.
* 서비스 사용자: 키 자격 증명 모음에 비밀으로 **서비스 사용자 키** 를 추가 합니다. 
* SQL 인증: 키 자격 증명 모음에 **암호를 암호로** 추가 합니다.
* 계정 키: 키 자격 증명 모음에 **계정 키** 를 암호로 추가 합니다.

자세한 내용은 [Key Vault에 암호 추가](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault)를 참조 하세요.

키 자격 증명 모음에 비밀을 저장 한 후 자격 증명 명령 모음에서 + 새로 만들기를 선택 하 여 새 자격 증명을 만듭니다. 인증 방법을 선택 하 고 암호를 선택할 Key Vault 인스턴스를 선택 하는 등 필요한 정보를 제공 합니다. 모든 세부 정보를 입력 한 후 만들기를 클릭 합니다.

:::image type="content" source="media/manage-credentials/new-credential.png" alt-text="새 자격 증명":::

자격 증명 목록 보기에 새 자격 증명이 표시 되 고 사용할 준비가 되었는지 확인 합니다.

:::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="자격 증명 보기":::

## <a name="manage-your-key-vault-connections"></a>주요 자격 증명 모음 연결 관리

1. 이름으로 Key Vault 연결 검색/찾기

    :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="키 자격 증명 모음 검색":::

1. 하나 이상의 Key Vault 연결 삭제
 
    :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="키 자격 증명 모음 삭제":::

## <a name="manage-your-credentials"></a>자격 증명 관리

1. 이름으로 자격 증명 검색/찾기
  
2. 기존 자격 증명을 선택 하 고 업데이트 합니다.

3. 하나 이상의 자격 증명 삭제
