---
title: Azure Data Lake Storage Gen2의 액세스 제어에 대한 개요 | Microsoft Docs
description: Azure Data Lake Storage Gen2에서 액세스 제어가 작동하는 방식을 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 93c21656a768ae458572e0b4917412c8103b2f2d
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992218"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2의 액세스 제어

Azure Data Lake Storage Gen2는 AZURE 역할 기반 액세스 제어(RBAC) 및 POSIX와 같은 액세스 제어 목록(ACL)을 모두 지원하는 액세스 제어 모델을 구현합니다. 이 문서에서는 Data Lake Storage Gen2의 액세스 제어 모델에 대한 기본 사항을 요약하고 있습니다.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

RBAC는 역할 할당을 사용하여 *보안 주체에*사용 권한 집합을 효과적으로 적용합니다. *보안 주체는* Azure 리소스에 대한 액세스를 요청하는 AD(Azure Active Directory)에 정의된 사용자, 그룹, 서비스 주체 또는 관리되는 ID를 나타내는 개체입니다.

일반적으로 이러한 Azure 리소스는 최상위 리소스(예: Azure Storage 계정)로 제한됩니다. Azure 저장소의 경우 결과적으로 Azure Data Lake Storage Gen2에서 이 메커니즘이 컨테이너(파일 시스템) 리소스로 확장되었습니다.

저장소 계정 범위의 보안 주체에게 역할을 할당하는 방법을 알아보려면 [Azure 포털에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 권한 부여 를](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)참조하세요.

> [!NOTE]
> 게스트 사용자는 역할 할당을 만들 수 없습니다.

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>역할 할당이 파일 및 디렉터리 수준 액세스 제어 목록에 미치는 영향

RBAC 역할 할당을 사용하는 것은 액세스 권한을 제어하는 강력한 메커니즘이지만 ACL에 비해 매우 거칠게 세분화된 메커니즘입니다. RBAC에 대한 가장 작은 세분성은 컨테이너 수준에 있으며 이는 ACL보다 더 높은 우선 순위로 평가됩니다. 따라서 컨테이너 범위의 보안 주체에 역할을 할당하는 경우 해당 보안 주체는 ACL 할당에 관계없이 해당 컨테이너의 모든 디렉터리 및 파일에 대해 해당 역할과 연결된 권한 부여 수준을 갖습니다.

보안 주체가 [기본 제공 역할을](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)통해 또는 사용자 지정 역할을 통해 RBAC 데이터 권한을 부여하면 요청 권한 부여 시 먼저 이러한 사용 권한이 평가됩니다. 요청된 작업이 보안 주체의 RBAC 할당에 의해 승인된 경우 권한 부여가 즉시 해결되고 추가 ACL 검사가 수행되지 않습니다. 또는 보안 주체에 RBAC 할당이 없거나 요청한 작업이 할당된 사용 권한과 일치하지 않는 경우 ACL 검사를 통해 보안 주체가 요청된 작업을 수행할 수 있는 권한이 있는지 확인합니다.

> [!NOTE]
> 보안 주체가 저장소 Blob 데이터 소유자 기본 제공 역할 할당을 할당한 경우 보안 주체는 *수퍼 사용자로* 간주되며 디렉터리 또는 파일의 소유자 설정과 소유자가 아닌 디렉터리 및 파일에 대한 ACL을 비롯한 모든 변이 작업에 대한 전체 액세스 권한이 부여됩니다. 슈퍼 사용자 액세스는 리소스 소유자를 변경할 수 있는 권한을 부여받는 유일한 방법입니다.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>공유 키 및 공유 액세스 서명(SAS) 인증

Azure Data Lake 저장소 Gen2는 인증을 위해 공유 키 및 SAS 메서드를 지원합니다. 이러한 인증 방법의 특징은 호출자와 연결된 ID가 없으므로 보안 주체 권한 기반 권한 권한을 수행할 수 없다는 것입니다.

공유 키의 경우 호출자는 '수퍼 사용자' 액세스를 효과적으로 얻으며, 이는 소유자 설정 및 ACL 변경을 비롯한 모든 리소스의 모든 작업에 대한 전체 액세스 권한을 의미합니다.

SAS 토큰에는 토큰의 일부로 허용된 권한이 포함됩니다. SAS 토큰에 포함된 권한은 모든 권한 부여 결정에 효과적으로 적용되지만, 추가적인 ACL 검사는 수행되지 않습니다.

## <a name="access-control-lists-on-files-and-directories"></a>파일 및 디렉터리에 대한 액세스 제어 목록

보안 보안 주체를 파일 및 디렉터리에 대한 액세스 수준과 연결할 수 있습니다. 이러한 연결은 *ACL(액세스 제어 목록)에*캡처됩니다. 저장소 계정의 각 파일 및 디렉터리에는 액세스 제어 목록이 있습니다.

> [!NOTE]
> ACL은 동일한 테넌트의 보안 주체에만 적용됩니다. 

저장소 계정 수준에서 보안 주체에 역할을 할당한 경우 액세스 제어 목록을 사용하여 특정 파일 및 디렉터리에 대한 보안 주체의 높은 액세스 권한을 부여할 수 있습니다.

역할 할당에 의해 부여된 수준보다 낮은 액세스 수준을 제공하기 위해 액세스 제어 목록을 사용할 수 없습니다. 예를 들어 [저장소 Blob 데이터 기여자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) 역할을 보안 보안 주체에 할당하는 경우 액세스 제어 목록을 사용하여 해당 보안 주체가 디렉터리에 쓰지 못하도록 할 수 없습니다.


### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>액세스 제어 목록을 사용하여 파일 및 디렉터리 수준 사용 권한 설정

파일 및 디렉터리 수준 사용 권한을 설정하려면 다음 문서를 참조하십시오.

|||
|--------|-----------|
|Azure Storage Explorer |[Azure 저장소 탐색기를 사용하여 Azure 데이터 레이크 저장소 Gen2에서 디렉터리, 파일 및 ACL을 관리합니다.](data-lake-storage-explorer.md#managing-access)|
|.NET |[.NET을 사용하여 Azure 데이터 레이크 저장소 Gen2에서 디렉터리, 파일 및 ACL관리](data-lake-storage-directory-file-acl-dotnet.md)|
|Java|[Java를 사용하여 Azure 데이터 레이크 저장소 Gen2에서 디렉터리, 파일 및 ACL 관리](data-lake-storage-directory-file-acl-java.md)|
|Python|[파이썬을 사용하여 Azure 데이터 레이크 스토리지 Gen2에서 디렉터리, 파일 및 ACL 관리](data-lake-storage-directory-file-acl-python.md)|
|PowerShell|[PowerShell을 사용하여 Azure 데이터 레이크 저장소 Gen2에서 디렉터리, 파일 및 ACL관리](data-lake-storage-directory-file-acl-powershell.md)|
|Azure CLI|[Azure CLI를 사용하여 Azure 데이터 레이크 저장소 Gen2에서 디렉터리, 파일 및 ACL을 관리합니다.](data-lake-storage-directory-file-acl-cli.md)|
|REST API |[경로 - 업데이트](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> 보안 주체가 *서비스* 주체인 경우 관련 앱 등록의 개체 ID가 아닌 서비스 주체의 개체 ID를 사용하는 것이 중요합니다. 서비스 주체의 개체 ID를 얻으려면 Azure CLI를 열고 `az ad sp show --id <Your App ID> --query objectId`다음 이 명령을 사용합니다. `<Your App ID>` 자리 표시자를 앱 등록의 앱 ID로 바꿔야 합니다.

### <a name="types-of-access-control-lists"></a>액세스 제어 목록의 유형

액세스 제어 목록에는 *ACL* 및 기본 *ACL의*두 가지 종류가 있습니다.

액세스 ACL은 개체에 대한 액세스를 제어합니다. 파일과 디렉터리 모두에 액세스 ACL이 있습니다.

기본 ACL은 해당 디렉터리 아래에 만들어진 모든 자식 항목에 대한 액세스 ACL을 결정하는 디렉터리와 연결된 ACL의 템플릿입니다. 파일에는 기본 ACL이 없습니다.

액세스 ACL 및 기본 ACL의 구조는 모두 동일합니다.

> [!NOTE]
> 부모 항목에서 기본 ACL을 변경해도 이미 존재하는 자식 항목의 액세스 ACL 또는 기본 ACL에는 영향을 주지 않습니다.

### <a name="levels-of-permission"></a>권한 수준

컨테이너 개체의 사용 권한은 **읽기,** **쓰기**및 **실행이며**다음 표와 같이 파일 및 디렉터리에서 사용할 수 있습니다.

|            |    파일     |   디렉터리 |
|------------|-------------|----------|
| **읽기(R)** | 파일의 내용을 읽을 수 있습니다. | 디렉터리의 내용을 나열하려면 **읽기** 및 **실행**이 필요합니다. |
| **쓰기(W)** | 쓰거나 파일에 추가할 수 있습니다. | 디렉터리에 자식 항목을 만들려면 **쓰기** 및 **실행**이 필요합니다. |
| **실행(X)** | Data Lake Storage Gen2의 컨텍스트에서는 의미가 없습니다. | 디렉터리의 자식 항목을 트래버스하는 데 필요합니다. |

> [!NOTE]
> ACL(RBAC 없음)만 사용하여 권한을 부여하는 경우 보안 주체가 파일에 대한 읽기 또는 쓰기 액세스 권한을 부여하려면 **Execute** 보안 주체에게 컨테이너 및 파일로 이어지는 폴더 계층구조의 각 폴더에 사용 권한을 부여해야 합니다.

#### <a name="short-forms-for-permissions"></a>사용 권한에 대한 짧은 형식

**RWX**는 **읽기 + 쓰기 + 실행**을 나타내는 데 사용됩니다. **읽기=4**, **쓰기=2** 및 **실행=1**의 압축된 숫자 형식이 있으며, 그 합계는 권한을 나타냅니다. 다음은 몇 가지 예입니다.

| 숫자 형식 | 짧은 형식 |      의미     |
|--------------|------------|------------------------|
| 7            | `RWX`        | 읽기 + 쓰기 + 실행 |
| 5            | `R-X`        | 읽기 + 실행         |
| 4            | `R--`        | 읽기                   |
| 0            | `---`        | 사용 권한 없음         |

#### <a name="permissions-inheritance"></a>권한 상속

Data Lake Storage Gen2에서 사용하는 POSIX 스타일 모델에서 항목에 대한 권한은 항목 자체에 저장됩니다. 즉 자식 항목이 이미 만들어진 후에 권한이 설정되면 항목에 대한 권한은 부모 항목에서 상속할 수 없습니다. 자식 항목이 만들어지기 전에 부모 항목에 기본 권한이 설정된 경우에만 권한이 상속됩니다.

### <a name="common-scenarios-related-to-permissions"></a>사용 권한과 관련된 일반적인 시나리오

다음 표에는 저장소 계정에서 특정 작업을 수행하는 데 필요한 권한을 이해하는 데 도움이 되는 몇 가지 일반적인 시나리오가 나열되어 있습니다.

|    작업(Operation)             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Read Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Append to Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Delete Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Create Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| List /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| List /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| List /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> 위의 두 조건이 참(true)이면 파일을 삭제하는 데 파일에 대한 쓰기 권한이 필요하지 않습니다.

### <a name="users-and-identities"></a>사용자 및 ID

모든 파일 및 디렉터리에는 이러한 ID에 대한 고유 권한이 있습니다.

- 소유 그룹
- 소유 그룹
- 명명된 사용자
- 명명된 그룹
- 명명된 서비스 주체
- 명명된 관리되는 ID
- 기타 모든 사용자

사용자 및 그룹의 ID는 Azure AD(Azure Active Directory) ID입니다. 따라서 달리 명시되지 않는 한 *사용자는*Data Lake Storage Gen2의 컨텍스트에서 Azure AD 사용자, 서비스 주체, 관리되는 ID 또는 보안 그룹을 참조할 수 있습니다.

#### <a name="the-owning-user"></a>소유 그룹

항목을 만든 사용자는 자동으로 항목의 소유 사용자가 됩니다. 소유 사용자는 다음을 수행할 수 있습니다.

* 소유한 파일의 권한을 변경합니다.
* 소유 사용자가 대상 그룹의 멤버이면 소유한 파일의 소유 그룹을 변경합니다.

> [!NOTE]
> 소유 사용자는 파일 또는 디렉터리의 소유 사용자를 *변경할 수 없습니다*. 슈퍼 사용자만 파일 또는 디렉터리의 소유 사용자를 변경할 수 있습니다.

#### <a name="the-owning-group"></a>소유 그룹

POSIX ACL에서 모든 사용자는 기본 *그룹과*연결됩니다. 예를 들어 사용자 "Alice"는 "재무" 그룹에 속할 수 있습니다. Alice는 여러 그룹에 속할 수도 있지만 한 그룹은 항상 기본 그룹으로 지정됩니다. POSIX에서 Alice가 파일을 만들 때는 해당 파일의 소유 그룹이 자신의 주 그룹(여기서는 "finance"임)으로 설정됩니다. 그렇지 않으면 소유 그룹은 다른 사용자/그룹에 할당된 사용 권한과 유사하게 동작합니다.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>새 파일 또는 디렉터리에 대한 소유 그룹 할당

* **사례 1**: 루트 디렉토리 "/". 이 디렉터리데이터 레이크 스토리지 Gen2 컨테이너를 만들 때 만들어집니다. 이 경우 소유 그룹은 OAuth를 사용하여 컨테이너를 만든 사용자로 설정됩니다. 공유 키, 계정 SAS 또는 서비스 SAS를 사용하여 컨테이너를 만든 경우 소유자 및 소유 그룹은 **$superuser.**
* **사례 2(다른** 모든 경우): 새 항목이 만들어지면 소유 그룹이 부모 디렉터리에서 복사됩니다.

##### <a name="changing-the-owning-group"></a>소유 그룹 변경

소유 그룹은 다음에 의해 변경될 수 있습니다.
* 모든 슈퍼 사용자
* 소유 사용자가 대상 그룹의 구성원이기도 한 경우 소유 사용자입니다.

> [!NOTE]
> 소유 그룹은 파일 또는 디렉터리의 ACL을 변경할 수 없습니다.  소유 그룹은 루트 디렉터리의 경우 계정을 만든 사용자로 설정되어 있지만 위의 **Case 1에서는** 단일 사용자 계정이 소유 그룹을 통해 권한을 제공하는 데 적합하지 않습니다. 해당하는 경우 올바른 사용자 그룹에 이 권한을 할당할 수 있습니다.

### <a name="access-check-algorithm"></a>액세스 검사 알고리즘

다음 의사 코드는 저장소 계정에 대한 액세스 검사 알고리즘을 나타냅니다.

```console
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask isn't used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

# Handle named groups and owning group
member_count = 0
perms = 0
entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    member_count += 1
    perms | =  entry.permissions
if (member_count>0) :
return ((desired_perms & perms & mask ) == desired_perms)

# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

#### <a name="the-mask"></a>마스크

액세스 검사 알고리즘에서 설명한 대로 마스크는 명명된 사용자, 소유 그룹 및 명명된 그룹에 대한 액세스를 제한합니다.  

> [!NOTE]
> 새 Data Lake Storage Gen2 컨테이너의 경우 루트 디렉터리("/")의 ACL 에 대한 마스크는 디렉토리의 경우 750이고 파일의 경우 640으로 기본설정됩니다. 저장소 전용 시스템의 파일과는 관련이 없으므로 파일은 X비트를 받지 않습니다.
>
> 마스크는 호출별로 지정할 수 있습니다. 이렇게 하면 클러스터와 같은 다양한 소비 시스템에서 파일 작업에 대한 다른 유효한 마스크를 갖출 수 있습니다. 지정된 요청에 마스크가 지정되면 기본 마스크를 완전히 재정의합니다.

#### <a name="the-sticky-bit"></a>고정 비트

끈적 끈적한 비트는 POSIX 컨테이너의 고급 기능입니다. Data Lake Storage Gen2의 컨텍스트에서는 고정 비트가 필요하지 않을 수 있습니다. 요약하면, 디렉터리에서 고정 비트를 사용하도록 설정되는 경우 자식 항목의 소유 사용자만 자식 항목을 삭제하거나 이름을 바꿀 수 있습니다.

고정 비트는 Azure 포털에 표시되지 않습니다.

### <a name="default-permissions-on-new-files-and-directories"></a>새 파일 및 디렉터리에 대한 기본 권한

기존 디렉터리 아래에 새 파일 또는 디렉터리를 만들어지면 부모 디렉터리에 대한 기본 ACL에서 다음 항목을 결정합니다.

- 자식 디렉토리의 기본 ACL 및 ACL에 액세스합니다.
- 자식 파일의 액세스 ACL(파일에 기본 ACL이 없는 경우)

#### <a name="umask"></a>umask

파일 또는 디렉터리를 만들 때는 umask를 사용하여 자식 항목에 기본 ACL이 설정되는 방식을 수정합니다. umask는 **소유 사용자**, **소유 그룹** 및 **기타**에 대한 RWX 값이 포함된 부모 디렉터리의 9비트 값입니다.

Azure Data Lake Storage Gen2에 대한 umask는 007로 설정된 상수 값입니다. 이 값은 다음과 같이 변환됩니다.

| umask 구성 요소     | 숫자 형식 | 짧은 형식 | 의미 |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | 소유 사용자의 경우 부모 항목의 기본 ACL을 자식 항목의 액세스 ACL에 복사합니다. | 
| umask.owning_group  |    0         |   `---`      | 소유 그룹의 경우 부모 항목의 기본 ACL을 자식 항목의 액세스 ACL에 복사합니다. | 
| umask.other         |    7         |   `RWX`      | 기타의 경우 자식 항목의 액세스 ACL에 있는 모든 권한을 제거합니다. |

Azure Data Lake Storage Gen2에서 효과적으로 사용되는 umask 값은 기본 ACL에서 나타내는 것과 관계없이 **기타**에 대한 값이 기본적으로 새 자식 항목에 전송되지 않는다는 것을 의미합니다. 

다음 의사 코드는 하위 항목의 ACL을 만들 때 umask가 적용되는 방식을 보여 줍니다.

```console
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Data Lake Storage Gen2의 ACL에 대한 일반적인 질문

### <a name="do-i-have-to-enable-support-for-acls"></a>ACL에 대한 지원을 사용하도록 설정해야 하나요?

아니요. 계층적 네임스페이스(HNS) 기능이 켜져 있는 한 ACL을 통한 액세스 제어가 저장소 계정에 활성화됩니다.

HNS가 해제된 경우에도 Azure RBAC 권한 부여 규칙이 여전히 적용됩니다.

### <a name="what-is-the-best-way-to-apply-acls"></a>ACL을 적용하는 가장 좋은 방법은 무엇인가요?

항상 Azure AD 보안 그룹을 ACL에 할당된 주체로 사용합니다. 개별 사용자 또는 서비스 주체를 직접 할당할 수 있는 기회를 거부합니다. 이 구조를 사용하면 ACL을 전체 디렉터리 구조에 다시 적용하지 않고도 사용자 또는 서비스 주체를 추가하고 제거할 수 있습니다. 그 대신 적절한 Azure AD 보안 그룹에서 추가하거나 제거해야 합니다. ACL은 상속되지 않으므로 ACL을 다시 적용하려면 모든 파일 및 하위 디렉터리에 대한 ACL을 업데이트해야 합니다. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>디렉터리 및 해당 내용을 재귀적으로 삭제하는 데 필요한 권한은 무엇인가요?

- 호출자에게 '수퍼 사용자' 권한이 있습니다.

Or

- 부모 디렉터리에 쓰기 + 실행 권한이 있어야 합니다.
- 삭제할 디렉터리와 그 안의 모든 디렉터리에 읽기 + 쓰기 + 실행 권한이 필요합니다.

> [!NOTE]
> 디렉터리의 파일을 삭제하는 데에는 쓰기 권한이 필요하지 않습니다. 또한 루트 디렉터리("/")는 절대로 삭제할 수 없습니다.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>파일 또는 디렉터리의 소유자는 누구인가요?

파일 또는 디렉터리의 작성자는 소유자가 됩니다. 루트 디렉터리에서 컨테이너를 만든 사용자의 ID입니다.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>만드는 파일 또는 디렉터리의 소유 그룹으로 설정되는 그룹은 무엇인가요?

소유 그룹은 새 파일 또는 디렉터리가 만들어지는 부모 디렉터리의 소유 그룹에서 복사됩니다.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>파일의 소유 사용자이지만 필요한 RWX 권한이 없습니다. 어떻게 해야 합니까?

소유 사용자는 파일의 권한을 변경하여 필요한 RWX 권한을 부여할 수 있습니다.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>ACL에 GUID가 표시되는 경우가 있습니다. 왜 그런가요?

항목이 사용자를 나타내고 해당 사용자가 Azure AD에 더 이상 존재하지 않으면 GUID가 표시됩니다. 일반적으로 사용자가 퇴사하거나 Azure AD에서 해당 계정이 삭제될 때 이러한 현상이 발생합니다. 또한 서비스 주체와 보안 그룹은 이를 식별할 UPN(사용자 계정 이름)을 가지고 있지 않으므로 OID 특성(GUID)으로 표시됩니다.

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>서비스 주체에 대해 ACL을 올바르게 설정하려면 어떻게 해야 합니까?

서비스 주체에 대한 ACL을 정의할 때는 서비스 *주체의* 개체 ID(OID)를 만든 앱 등록에 사용하는 것이 중요합니다. 등록된 앱에는 특정 Azure AD 테넌트에 별도의 서비스 주체가 있다는 점에 유의해야 합니다. 등록된 앱에는 Azure 포털에 표시되는 OID가 있지만 *서비스 주체에는* 다른 (다른) OID가 있습니다.

앱 등록에 해당하는 서비스 주체에 대한 OID를 얻으려면 `az ad sp show` 명령을 사용할 수 있습니다. 응용 프로그램 ID를 매개 변수로 지정합니다. 다음은 앱 ID = 18218b12-1895-1895-43e9-ad80-6e8fc1ea88ce를 사용하여 앱 등록에 해당하는 서비스 주체에 대한 OID를 얻는 예제입니다. Azure CLI에서 다음 명령을 실행합니다.

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

OID가 표시됩니다.

서비스 주체에 대한 올바른 OID가 있는 경우 저장소 탐색기 **액세스 관리** 페이지로 이동하여 OID를 추가하고 OID에 대한 적절한 권한을 할당합니다. **저장을**선택해야 합니다.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage Gen2에서 ACL의 상속을 지원하나요?

Azure RBAC 할당은 상속됩니다. 할당은 구독, 리소스 그룹 및 저장소 계정 리소스에서 컨테이너 리소스로 이동합니다.

ACL은 상속되지 않습니다. 그러나 기본 ACL을 사용하여 부모 디렉터리 아래에 만들어진 자식 하위 디렉터리 및 파일에 대한 ACL을 설정할 수 있습니다. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>POSIX 액세스 제어 모델에 대한 어디서 자세히 알아볼 수 있나요?

* [Linux의 POSIX Access Control 목록](https://www.linux.com/news/posix-acls-linux)(영문)
* [HDFS 권한 가이드](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)(영문)
* [POSIX FAQ](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [Ubuntu의 POSIX ACL](https://help.ubuntu.com/community/FilePermissionsACLs)
* [리눅스에서 액세스 제어 목록을 사용 하 여 ACL](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>참조

* [Azure Data Lake Storage Gen2 개요](../blobs/data-lake-storage-introduction.md)
