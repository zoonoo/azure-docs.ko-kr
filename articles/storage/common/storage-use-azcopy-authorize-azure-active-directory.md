---
title: AzCopy & Azure Active Directory를 사용 하 여 blob에 대 한 액세스 권한 부여 Microsoft Docs
description: Azure Active Directory (Azure AD)를 사용 하 여 AzCopy 작업에 대 한 권한 부여 자격 증명을 제공할 수 있습니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 89aab37b750a61bd21ba9af23875536a8cfbff4a
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414828"
---
# <a name="authorize-access-to-blobs-with-azcopy-and-azure-active-directory-azure-ad"></a>AzCopy 및 Azure Active Directory를 사용 하 여 blob에 대 한 액세스 권한 부여 (Azure AD)

Azure AD를 사용 하 여 권한 부여 자격 증명으로 AzCopy를 제공할 수 있습니다. 이렇게 하면 각 명령에 SAS (공유 액세스 서명) 토큰을 추가할 필요가 없습니다. 

역할 할당을 확인 하 여 시작 합니다. 그런 다음 권한 부여 하려는 _보안 주체의_ 유형을 선택 합니다. [사용자 id](../../active-directory/fundamentals/add-users-azure-active-directory.md), [관리 id](../../active-directory/managed-identities-azure-resources/overview.md)및 [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md) 는 각각 보안 주체의 유형입니다.

사용자 id는 Azure AD에서 id가 있는 모든 사용자입니다. 권한을 부여 하는 가장 쉬운 보안 주체입니다. 관리 id 및 서비스 주체는 사용자 개입 없이 실행 되는 스크립트 내에서 AzCopy를 사용 하려는 경우에 유용한 옵션입니다. 관리 id는 Azure VM (가상 컴퓨터)에서 실행 되는 스크립트에 보다 적합 하며, 서비스 주체는 온-프레미스에서 실행 되는 스크립트에 보다 적합 합니다. 

AzCopy에 대 한 자세한 내용은 [AzCopy 시작을 시작](storage-use-azcopy-v10.md)하세요.

## <a name="verify-role-assignments"></a>역할 할당 확인

필요한 권한 부여 수준은 파일을 업로드할 것인지 아니면 다운로드할지를 기준으로 합니다.

파일을 다운로드 하려는 경우에는 [저장소 Blob 데이터 판독기](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 역할이 사용자 id, 관리 id 또는 서비스 주체에 할당 되었는지 확인 합니다.

파일을 업로드 하려면 다음 역할 중 하나가 보안 주체에 할당 되었는지 확인 합니다.

- [Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- [Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

이러한 모든 범위에서 보안 주체에 이러한 역할을 할당할 수 있습니다.

- 컨테이너 (파일 시스템)
- 스토리지 계정
- Resource group
- Subscription

역할을 확인 하 고 할당 하는 방법을 알아보려면 [Azure Portal를 사용 하 여 blob 및 큐 데이터에 액세스 하기 위한 Azure 역할 할당](./storage-auth-aad-rbac-portal.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)을 참조 하세요.

> [!NOTE]
> Azure 역할 할당을 전파 하는 데 최대 5 분이 걸릴 수 있다는 점에 유의 하세요.

보안 주체를 대상 컨테이너 또는 디렉터리의 ACL (액세스 제어 목록)에 추가 하는 경우 이러한 역할 중 하나를 보안 주체에 할당 하지 않아도 됩니다. ACL에서 보안 주체에는 대상 디렉터리에 대 한 쓰기 권한과 컨테이너 및 각 부모 디렉터리에 대 한 실행 권한이 필요 합니다.

자세히 알아보려면 [Azure Data Lake Storage Gen2의 액세스 제어 모델](../blobs/data-lake-storage-access-control-model.md)을 참조 하세요.

## <a name="authorize-a-user-identity"></a>사용자 id 권한 부여

사용자 id에 필요한 권한 부여 수준이 지정 되었는지 확인 한 후 명령 프롬프트를 열고 다음 명령을 입력 한 다음 ENTER 키를 누릅니다.

```azcopy
azcopy login
```

오류가 표시 되 면 저장소 계정이 속한 조직의 테 넌 트 ID를 포함 해 보세요.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

`<tenant-id>`자리 표시자를 저장소 계정이 속한 조직의 테 넌 트 ID로 바꿉니다. 테 넌 트 ID를 찾으려면 Azure Portal에서 **디렉터리 id > Azure Active Directory > 속성** 을 선택 합니다.

이 명령은 웹 사이트의 인증 코드와 URL을 반환합니다. 웹 사이트를 열고, 코드를 입력하고, **다음** 단추를 선택합니다.

![컨테이너 만들기](media/storage-use-azcopy-v10/azcopy-login.png)

로그인 창이 나타납니다. 이 창에서 Azure 계정 자격 증명을 사용하여 Azure 계정에 로그인합니다. 로그인에 성공하면 브라우저 창을 닫고 AzCopy를 사용할 수 있습니다.

<a id="service-principal"></a>

## <a name="authorize-a-managed-identity"></a>관리 ID 권한 부여

사용자 개입 없이 실행 되는 스크립트 내에서 AzCopy를 사용 하 고 Azure VM (가상 컴퓨터)에서 스크립트를 실행 하려는 경우이 옵션을 사용 하는 것이 좋습니다. 이 옵션을 사용 하는 경우 VM에 자격 증명을 저장할 필요가 없습니다.

VM에서 사용 하도록 설정한 시스템 차원의 관리 되는 id를 사용 하거나 VM에 할당 된 사용자 할당 관리 id의 클라이언트 ID, 개체 ID 또는 리소스 ID를 사용 하 여 계정에 로그인 할 수 있습니다.

시스템 차원의 관리 되는 id를 사용 하도록 설정 하는 방법 또는 사용자 할당 관리 id를 만드는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 VM에서 Azure 리소스에 대 한 관리 되는 Id 구성](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)을 참조 하세요.

#### <a name="authorize-by-using-a-system-wide-managed-identity"></a>시스템 차원의 관리 되는 id를 사용 하 여 권한 부여

먼저 VM에서 시스템 차원의 관리 되는 id를 사용 하도록 설정 했는지 확인 합니다. [시스템 할당 관리 id](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)를 참조 하십시오.

그런 다음 명령 콘솔에서 다음 명령을 입력 하 고 ENTER 키를 누릅니다.

```azcopy
azcopy login --identity
```

#### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>사용자 할당 관리 id를 사용 하 여 권한 부여

먼저 VM에서 사용자 할당 관리 id를 사용 하도록 설정 했는지 확인 합니다. [사용자 할당 관리 id](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity)를 참조 하십시오.

그런 다음 명령 콘솔에서 다음 명령 중 하나를 입력 하 고 ENTER 키를 누릅니다.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

`<client-id>`자리 표시자를 사용자 할당 관리 id의 클라이언트 ID로 바꿉니다.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

`<object-id>`자리 표시자를 사용자 할당 관리 id의 개체 ID로 바꿉니다.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

`<resource-id>`자리 표시자를 사용자 할당 관리 id의 리소스 ID로 바꿉니다.

## <a name="authorize-a-service-principal"></a>서비스 사용자 권한 부여

이 옵션은 특히 온-프레미스에서 실행 하는 경우 사용자 개입 없이 실행 되는 스크립트 내에서 AzCopy를 사용 하려는 경우에 유용 합니다. Azure에서 실행 되는 Vm에서 AzCopy를 실행 하려는 경우 관리 되는 서비스 id를 관리 하기가 더 쉽습니다. 자세히 알아보려면이 문서의 [관리 Id 권한 부여](#authorize-a-managed-identity) 섹션을 참조 하세요.

스크립트를 실행 하기 전에 AzCopy에 서비스 주체의 자격 증명을 제공할 수 있도록 대화형으로 한 번 이상 로그인 해야 합니다.  이러한 자격 증명은 스크립트가 중요 한 정보를 제공할 필요가 없도록 보안 및 암호화 된 파일에 저장 됩니다.

클라이언트 암호를 사용 하거나 서비스 주체의 앱 등록과 연결 된 인증서의 암호를 사용 하 여 계정에 로그인 할 수 있습니다.

서비스 주체를 만드는 방법에 대해 자세히 알아보려면 [방법: 포털을 사용 하 여 리소스에 액세스할 수 있는 AZURE AD 응용 프로그램 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)를 참조 하세요.

일반적으로 서비스 주체에 대 한 자세한 내용은 [Azure Active Directory의 응용 프로그램 및 서비스 주체 개체](../../active-directory/develop/app-objects-and-service-principals.md) 를 참조 하세요.

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>클라이언트 암호를 사용 하 여 서비스 사용자 권한 부여

먼저 `AZCOPY_SPA_CLIENT_SECRET` 환경 변수를 서비스 주체의 앱 등록에 대 한 클라이언트 암호로 설정 합니다.

> [!NOTE]
> 운영 체제의 환경 변수 설정이 아니라 명령 프롬프트에서이 값을 설정 해야 합니다. 이렇게 하면 현재 세션에만 값을 사용할 수 있습니다.

이 예제에서는 PowerShell에서이 작업을 수행 하는 방법을 보여 줍니다.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> 이 예제에 나와 있는 것 처럼 프롬프트를 사용 하는 것이 좋습니다. 이렇게 하면 사용자의 암호가 콘솔의 명령 기록에 표시 되지 않습니다.  

다음 명령을 입력 하 고 ENTER 키를 누릅니다.

```azcopy
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

`<application-id>`자리 표시자를 서비스 사용자의 앱 등록의 응용 프로그램 ID로 바꿉니다. `<tenant-id>`자리 표시자를 저장소 계정이 속한 조직의 테 넌 트 ID로 바꿉니다. 테 넌 트 ID를 찾으려면 Azure Portal에서 **디렉터리 id > Azure Active Directory > 속성** 을 선택 합니다. 

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>인증서를 사용 하 여 서비스 주체 권한 부여

권한 부여를 위해 자체 자격 증명을 사용 하려는 경우 앱 등록에 인증서를 업로드 한 다음 해당 인증서를 사용 하 여 로그인 할 수 있습니다.

앱 등록에 인증서를 업로드 하는 것 외에도 AzCopy가 실행 되는 컴퓨터 또는 VM에 인증서 복사본을 저장 해야 합니다. 인증서 복사본은에 있어야 합니다. PFX 또는. PEM 형식 및에는 개인 키가 포함 되어야 합니다. 개인 키는 암호로 보호 되어야 합니다. Windows를 사용 하는 경우 인증서가 인증서 저장소에만 있는 경우 해당 인증서를 PFX 파일 (개인 키 포함)로 내보내야 합니다. 지침은 [get-pfxcertificate](/powershell/module/pkiclient/export-pfxcertificate) 를 참조 하세요.

그런 다음 `AZCOPY_SPA_CERT_PASSWORD` 환경 변수를 인증서 암호로 설정 합니다.

> [!NOTE]
> 운영 체제의 환경 변수 설정이 아니라 명령 프롬프트에서이 값을 설정 해야 합니다. 이렇게 하면 현재 세션에만 값을 사용할 수 있습니다.

이 예제에서는 PowerShell에서이 작업을 수행 하는 방법을 보여 줍니다.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

다음 명령을 입력 하 고 ENTER 키를 누릅니다.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

`<path-to-certificate-file>`자리 표시자를 인증서 파일의 상대 경로 또는 정규화 된 경로로 바꿉니다. AzCopy는이 인증서에 대 한 경로를 저장 하지만 인증서 복사본을 저장 하지 않으므로 해당 인증서를 그대로 유지 해야 합니다. `<tenant-id>`자리 표시자를 저장소 계정이 속한 조직의 테 넌 트 ID로 바꿉니다. 테 넌 트 ID를 찾으려면 Azure Portal에서 **디렉터리 id > Azure Active Directory > 속성** 을 선택 합니다.

> [!NOTE]
> 이 예제에 나와 있는 것 처럼 프롬프트를 사용 하는 것이 좋습니다. 이렇게 하면 사용자의 암호가 콘솔의 명령 기록에 표시 되지 않습니다. 

<a id="managed-identity"></a>


## <a name="next-steps"></a>다음 단계

- AzCopy에 대 한 자세한 내용은 [AzCopy 시작을 시작 하세요](storage-use-azcopy-v10.md) .

- 질문, 문제 또는 일반 피드백이 있는 경우 [GitHub 페이지에서](https://github.com/Azure/azure-storage-azcopy) 제출 합니다.