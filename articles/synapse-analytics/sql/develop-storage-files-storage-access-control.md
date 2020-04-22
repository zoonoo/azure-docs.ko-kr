---
title: SQL 주문형(미리 보기) 스토리지 계정 액세스 제어
description: SQL 주문형(미리 보기)에서 Azure Storage에 액세스하는 방법과 Azure Synapse Analytics에서 SQL 주문형 스토리지 액세스를 제어하는 방법을 설명합니다.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420057"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 SQL 주문형(미리 보기) 스토리지 계정 액세스 제어

SQL 주문형(미리 보기) 쿼리는 Azure Storage에서 직접 파일을 읽습니다. 스토리지 계정은 SQL 주문형 리소스 외부에 있는 개체이므로 적절한 자격 증명이 필요합니다. 사용자에게는 필수 자격 증명을 사용할 수 있도록 부여된 적절한 권한이 필요합니다. 이 문서에서는 사용할 수 있는 자격 증명의 유형과 SQL 및 Azure AD 사용자에 대한 자격 증명 조회가 적용되는 방법에 대해 설명합니다.

## <a name="supported-storage-authorization-types"></a>지원되는 스토리지 권한 부여 유형

SQL 주문형 리소스에 로그인한 사용자는 Azure Storage의 파일에 액세스하고 쿼리할 수 있는 권한이 있어야 합니다. 지원되는 세 가지 권한 부여 유형은 다음과 같습니다.

- [공유 액세스 서명](#shared-access-signature)
- [관리 ID](#managed-identity)
- [사용자 ID](#user-identity)

> [!NOTE]
> [Azure AD 통과](#force-azure-ad-pass-through)는 작업 영역을 만들 때의 기본 동작입니다. 이를 사용하는 경우 AD 로그인을 사용하여 액세스하는 각 스토리지 계정에 대한 자격 증명을 만들 필요가 없습니다. [이 동작을 사용하지 않도록 설정](#disable-forcing-azure-ad-pass-through)할 수 있습니다.

아래 표에는 지원되거나 곧 지원될 다양한 권한 부여 유형이 나와 있습니다.

| 권한 부여 유형                    | *SQL 사용자*    | *Azure AD 사용자*     |
| ------------------------------------- | ------------- | -----------    |
| [SAS](#shared-access-signature)       | 지원됨     | 지원됨      |
| [관리 ID](#managed-identity) | 지원되지 않음 | 지원되지 않음  |
| [사용자 ID](#user-identity)       | 지원되지 않음 | 지원됨      |

### <a name="shared-access-signature"></a>공유 액세스 서명

**SAS(공유 액세스 서명)** 는 스토리지 계정의 리소스에 대한 위임된 액세스를 제공합니다. SAS를 사용하면 고객이 계정 키를 공유하지 않고 스토리지 계정의 리소스에 대한 액세스 권한을 클라이언트에 부여할 수 있습니다. SAS는 유효성 간격, 부여된 권한, 허용되는 IP 주소 범위 및 허용되는 프로토콜(https/http)을 포함하여 SAS를 사용하는 클라이언트에 부여하는 액세스 유형에 대한 세부적인 제어를 제공합니다.

**Azure Portal > 스토리지 계정 > 공유 액세스 서명 > 권한 구성 > SAS 및 연결 문자열 생성**으로 차례로 이동하여 SAS 토큰을 가져올 수 있습니다.

> [!IMPORTANT]
> SAS 토큰이 생성되면 토큰의 시작 부분에 물음표('?')가 포함됩니다. SQL 주문형에서 토큰을 사용하려면 자격 증명을 만들 때 물음표('')를 제거해야 합니다. 다음은 그 예입니다.
>
> SAS token: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

### <a name="user-identity"></a>사용자 ID

**사용자 ID**("통과"라고도 함)는 주문형 SQL에 로그인한 Azure AD 사용자의 ID를 사용하여 데이터 액세스 권한을 부여하는 권한 부여 유형입니다. 데이터에 액세스하기 전에 Azure Storage 관리자가 Azure AD 사용자에게 권한을 부여해야 합니다. 위의 표에서 설명한 대로 SQL 사용자 유형에는 지원되지 않습니다.

> [!NOTE]
> [Azure AD 통과](#force-azure-ad-pass-through)를 사용하는 경우 AD 로그인을 사용하여 액세스하는 각 스토리지 계정에 대한 자격 증명을 만들 필요가 없습니다.

> [!IMPORTANT]
> 이 ID를 사용하여 데이터에 액세스하려면 Storage Blob 데이터 소유자/기여자/읽기 권한자 역할이 있어야 합니다.
> 스토리지 계정의 소유자인 경우에도 Storage Blob 데이터 역할 중 하나에 자신을 추가해야 합니다.
>
> Azure Data Lake Store Gen2의 액세스 제어에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](../../storage/blobs/data-lake-storage-access-control.md) 문서를 검토하세요.
>

### <a name="managed-identity"></a>관리 ID

**관리 ID**는 MSI라고도 합니다. Azure 서비스를 SQL 주문형에 제공하는 Azure AD(Azure Active Directory)의 기능입니다. 또한 Azure AD에서 관리 ID를 자동으로 배포합니다. 이 ID는 Azure Storage에서 데이터 액세스에 대한 요청을 승인하는 데 사용될 수 있습니다.

데이터에 액세스하기 전에 Azure Storage 관리자가 데이터에 액세스할 수 있는 권한을 관리 ID에 부여해야 합니다. 관리 ID에 대한 권한 부여는 다른 Azure AD 사용자에게 권한을 부여하는 것과 동일한 방식으로 수행됩니다.

## <a name="create-credentials"></a>자격 증명 만들기

Azure Storage에 있는 파일을 쿼리하려면 SQL 주문형 엔드포인트에 인증 정보가 포함된 서버 수준 CREDENTIAL이 필요합니다. 자격 증명은 [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)을 실행하여 추가됩니다. CREDENTIAL NAME 인수를 제공해야 합니다. Storage의 데이터에 대한 경로의 일부 또는 전체 경로와 일치해야 합니다(아래 참조).

> [!NOTE]
> FOR CRYPTOGRAPHIC PROVIDER 인수는 지원되지 않습니다.

지원되는 모든 권한 부여 유형의 경우 자격 증명은 계정, 컨테이너, 디렉터리(루트가 아님) 또는 단일 파일을 가리킬 수 있습니다.

CREDENTIAL NAME은 컨테이너, 폴더 또는 파일에 대한 `<prefix>://<storage_account_path>/<storage_path>` 형식의 전체 경로와 일치해야 합니다.

| 외부 데이터 원본       | 접두사 | 스토리지 계정 경로                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>.dfs.core.windows.net              |

 '<storage_path>'는 읽으려는 폴더 또는 파일을 가리키는 스토리지 내의 경로입니다.

> [!NOTE]
> [Azure AD 통과를 강제 적용](#force-azure-ad-pass-through)하는 특별한 CREDENTIAL NAME인 `UserIdentity`가 있습니다. 쿼리를 실행하는 동안 [자격 증명 조회](#credential-lookup)에 미치는 영향을 참조하세요.

필요에 따라 사용자가 자격 증명을 만들거나 삭제할 수 있도록 관리자는 사용자에게 GRANT/DENY ALTER ANY CREDENTIAL 권한을 부여할 수 있습니다.

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>지원되는 스토리지 및 권한 부여 유형

사용할 수 있는 권한 부여 및 Azure Storage 유형의 조합은 다음과 같습니다.

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SAS*               | 지원됨      | 지원되지 않음   | 지원됨     |
| *관리 ID* | 지원되지 않음  | 지원되지 않음    | 지원되지 않음 |
| *사용자 ID*    | 지원됨      | 지원됨        | 지원됨     |

### <a name="examples"></a>예

[권한 부여 유형](#supported-storage-authorization-types)에 따라 아래 T-SQL 구문을 사용하여 자격 증명을 만들 수 있습니다.

**공유 액세스 서명 및 Blob Storage**

<*mystorageaccountname*>을 실제 스토리지 계정 이름으로 교환하고, <*mystorageaccountcontainername*>을 실제 컨테이너 이름으로 교환합니다.

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**사용자 ID 및 Azure Data Lake Storage Gen1**

<*mystorageaccountname*>을 실제 스토리지 계정 이름으로 교환하고, <*mystorageaccountcontainername*>을 실제 컨테이너 이름으로 교환합니다.

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**사용자 ID 및 Azure Data Lake Storage Gen2**

<*mystorageaccountname*>을 실제 스토리지 계정 이름으로 교환하고, <*mystorageaccountcontainername*>을 실제 컨테이너 이름으로 교환합니다.

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>Azure AD 통과 강제 적용

Azure AD 통과를 강제 적용하는 것은 Azure Synapse 작업 영역을 프로비저닝하는 중에 자동으로 만들어지는 특수 CREDENTIAL NAME인 `UserIdentity`를 통해 수행되는 기본 동작입니다. 다른 자격 증명이 있음에도 불구하고 모든 Azure AD 로그인의 각 쿼리에 Azure AD 통과를 사용하도록 강제합니다.

> [!NOTE]
> Azure AD 통과는 기본 동작입니다. 따라서 AD 로그인을 통해 액세스하는 각 스토리지 계정에 대한 자격 증명을 만들 필요가 없습니다.

[Azure AD 통과 강제 적용을 각 쿼리에 사용하지 않도록 설정](#disable-forcing-azure-ad-pass-through)한 후에 이를 사용하도록 다시 설정하려면 다음을 실행합니다.

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Azure AD 통과를 특정 사용자에 강제 적용하도록 설정하려면 해당 특정 사용자에게 `UserIdentity` 자격 증명에 대한 REFERENCE 권한을 부여할 수 있습니다. 다음 예제에서는 Azure AD 통과를 user_name에 강제 적용하도록 설정합니다.

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

SQL 주문형에서 사용할 자격 증명을 찾는 방법에 대한 자세한 내용은 [자격 증명 조회](#credential-lookup)를 참조하세요.

## <a name="disable-forcing-azure-ad-pass-through"></a>Azure AD 통과 강제 적용 사용 안 함

[Azure AD 통과를 각 쿼리에 강제 적용](#force-azure-ad-pass-through)하지 않도록 설정할 수 있습니다. 사용하지 않도록 설정하려면 다음을 사용하여 `Userdentity` 자격 증명을 삭제합니다.

```sql
DROP CREDENTIAL [UserIdentity];
```

사용하도록 다시 설정하려면 [Azure AD 통과 강제 적용](#force-azure-ad-pass-through) 섹션을 참조하세요.

Azure AD 통과를 특정 사용자에 강제 적용하지 않도록 설정하려면 해당 특정 사용자의 `UserIdentity` 자격 증명에 대한 REFERENCE 권한을 거부할 수 있습니다. 다음 예제에서는 Azure AD 통과를 user_name에 강제 적용하지 않도록 설정합니다.

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

SQL 주문형에서 사용할 자격 증명을 찾는 방법에 대한 자세한 내용은 [자격 증명 조회](#credential-lookup)를 참조하세요.

## <a name="grant-permissions-to-use-credential"></a>자격 증명을 사용할 수 있는 권한 부여

자격 증명을 사용하려면 사용자에게 특정 자격 증명에 대한 REFERENCES 권한이 있어야 합니다. specific_user의 storage_credential에 대한 REFERENCES 권한을 부여하려면 다음을 실행합니다.

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

원활한 Azure AD 통과 환경을 보장하기 위해 기본적으로 모든 사용자에게 `UserIdentity` 자격 증명을 사용할 수 있는 권한이 있습니다. 이 작업은 Azure Synapse 작업 영역을 프로비저닝할 때 다음 명령문을 자동으로 실행하여 수행됩니다.

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>자격 증명 조회

쿼리에 권한을 부여할 때 자격 증명 조회는 스토리지 계정에 액세스하는 데 사용되며, 다음 규칙을 기반으로 합니다.

- 사용자가 Azure AD 로그인으로 로그인됨

  - UserIdentity 자격 증명이 있고 사용자에게 이에 대한 참조 권한이 있는 경우 Azure AD 통과가 사용됩니다. 그렇지 않은 경우 [경로별 자격 증명 조회](#lookup-credential-by-path)가 사용됩니다.

- 사용자가 SQL 로그인으로 로그인됨

  - [경로별 자격 증명 조회](#lookup-credential-by-path)가 사용됩니다.

### <a name="lookup-credential-by-path"></a>경로별 자격 증명 조회

Azure AD 통과를 강제 적용하지 않도록 설정하는 경우 자격 증명 조회는 스토리지 경로(깊이 우선) 및 해당 특정 자격 증명에 대한 REFERENCES 권한의 존재 여부를 기준으로 합니다. 동일한 파일에 액세스하는 데 사용할 수 있는 여러 자격 증명이 있는 경우 SQL 주문형은 가장 구체적인 자격 증명을 사용합니다.  

다음은 *account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX.ext* 파일 경로에 대한 쿼리의 예제입니다.

자격 증명 조회는 다음 순서로 수행됩니다.

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

사용자에게 자격 증명 번호 5에 대한 REFERENCES 권한이 없는 경우 SQL 주문형은 사용자에게 REFERENCES 권한이 있는 자격 증명을 찾을 때까지 사용자에게 한 수준 높은 자격 증명에 대한 REFERENCES 권한이 있는지 확인합니다. 이러한 권한이 없으면 오류 메시지가 반환됩니다.

### <a name="credential-and-path-level"></a>자격 증명 및 경로 수준

원하는 경로 셰이프에 따라 쿼리를 실행하기 위해 다음 요구 사항이 있습니다.

- 쿼리에서 여러 파일(와일드카드를 사용하거나 사용하지 않는 폴더)을 대상으로 하는 경우 사용자는 루트 디렉터리 수준(컨테이너 수준) 이상의 자격 증명에 액세스할 수 있어야 합니다. 목록 파일은 루트 디렉터리에서 상대적으로 이 액세스 수준이 필요합니다(Azure Storage 제한 사항).
- 쿼리에서 단일 파일을 대상으로 하는 경우 SQL 주문형에서 파일에 직접 액세스하므로(즉, 폴더를 나열하지 않음) 사용자는 모든 수준의 자격 증명에 액세스할 수 있어야 합니다.

|                  | *계정* | *루트 디렉터리* | *다른 모든 디렉터리* | *최근에 사용한 파일*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *단일 파일*    | 지원됨 | 지원됨        | 지원됨             | 지원됨     |
| *여러 파일* | 지원됨 | 지원됨        | 지원되지 않음         | 지원되지 않음 |

## <a name="next-steps"></a>다음 단계

아래 나열된 문서를 통해 다른 폴더 유형, 파일 형식을 쿼리하고, 보기를 만들고 사용하는 방법을 알아볼 수 있습니다.

- [단일 CSV 파일 쿼리](query-single-csv-file.md)
- [폴더 및 여러 CSV 파일 쿼리](query-folders-multiple-csv-files.md)
- [특정 파일 쿼리](query-specific-files.md)
- [Parquet 파일 쿼리](query-parquet-files.md)
- [보기 만들기 및 사용](create-use-views.md)
- [JSON 파일 쿼리](query-json-files.md)
- [Parquet 중첩 형식 쿼리](query-parquet-nested-types.md)
