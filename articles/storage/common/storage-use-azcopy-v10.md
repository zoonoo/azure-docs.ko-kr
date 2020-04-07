---
title: AzCopy v10 | 사용하여 데이터를 Azure 저장소로 복사하거나 이동 마이크로 소프트 문서
description: AzCopy는 저장소 계정 간에 데이터를 복사하거나 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 도움말에서는 AzCopy를 다운로드하고 저장소 계정에 연결한 다음 파일을 전송하는 데 도움이 됩니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8701fe6857e95334a5e1d24bfe70feb130d5512c
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756019"
---
# <a name="get-started-with-azcopy"></a>AzCopy 시작

AzCopy는 저장소 계정에서 Blob 또는 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 도움말에서는 AzCopy를 다운로드하고 저장소 계정에 연결한 다음 파일을 전송하는 데 도움이 됩니다.

> [!NOTE]
> AzCopy **V10은** 현재 지원되는 AzCopy 버전입니다.
>
> 이전 버전의 AzCopy를 사용해야 하는 경우 이 [문서의 이전 버전 AzCopy 사용](#previous-version) 섹션을 참조하십시오.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>AzCopy 다운로드

먼저 컴퓨터의 모든 디렉터리에 AzCopy V10 실행 파일을 다운로드합니다. AzCopy V10은 실행 파일일 뿐이므로 설치할 파일이 없습니다.

- [윈도우 64 비트](https://aka.ms/downloadazcopy-v10-windows) (우편)
- [윈도우 32 비트](https://aka.ms/downloadazcopy-v10-windows-32bit) (우편)
- [Linux](https://aka.ms/downloadazcopy-v10-linux)(tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac)(zip)

이 파일은 zip 파일 (윈도우와 맥) 또는 타르 파일 (리눅스)로 압축된다. 리눅스에서 타르 파일을 다운로드하고 압축을 풀려면 Linux 배포판에 대한 설명서를 참조하십시오.

> [!NOTE]
> [Azure 테이블 저장소](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) 서비스에서 데이터를 복사하려면 [AzCopy 버전 7.3을](https://aka.ms/downloadazcopynet)설치합니다.


## <a name="run-azcopy"></a>아즈카피 실행

편의를 위해 사용 편의를 위해 시스템 경로에 AzCopy 실행 의 디렉터리 위치를 추가하는 것이 좋습니다. 이렇게 하면 시스템의 `azcopy` 모든 디렉터리에서 입력할 수 있습니다.

경로에 AzCopy 디렉터리를 추가하지 않도록 선택한 경우 디렉터리를 AzCopy 실행 및 입력 `azcopy` 또는 `.\azcopy` Windows PowerShell 명령 프롬프트의 위치로 변경해야 합니다.

명령 목록을 보려면 ENTER 키를 입력한 `azcopy -h` 다음 누릅니다.

특정 명령에 대해 알아보려면 명령의 이름을 포함하기만 `azcopy list -h`하면 됩니다(예: ).

![인라인 도움말](media/storage-use-azcopy-v10/azcopy-inline-help.png)

각 명령 및 명령 매개 변수에 대한 자세한 참조 문서를 찾으려면 [azcopy를](storage-ref-azcopy.md) 참조하십시오.

> [!NOTE] 
> Azure Storage 계정의 소유자는 데이터에 액세스할 수 있는 권한이 자동으로 할당되지 않습니다. AzCopy를 사용하여 의미 있는 작업을 수행하려면 저장소 서비스에 권한 부여 자격 증명을 제공하는 방법을 결정해야 합니다. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>권한 부여 자격 증명을 제공하는 방법 선택

Azure Active Directory(AD)를 사용하거나 SAS(공유 액세스 서명) 토큰을 사용하여 권한 부여 자격 증명을 제공할 수 있습니다.

이 표를 다음과 같이 하십시오.

| 스토리지 유형 | 현재 지원되는 권한 부여 방법 |
|--|--|
|**Blob Storage** | Azure AD & SAS |
|**Blob 저장소(계층적 네임스페이스)** | Azure AD & SAS |
|**파일 저장** | SAS 전용 |

### <a name="option-1-use-azure-active-directory"></a>옵션 1: Azure 활성 디렉터리 사용

Azure Active Directory를 사용하면 각 명령에 SAS 토큰을 추가할 필요 없이 자격 증명을 한 번 제공할 수 있습니다.  

> [!NOTE]
> 현재 릴리스에서 저장소 계정 간에 Blob을 복사하려는 경우 각 원본 URL에 SAS 토큰을 부가해야 합니다. 대상 URL에서만 SAS 토큰을 생략할 수 있습니다. 예제에서는 저장소 [계정 간에 Blob 복사를](storage-use-azcopy-blobs.md)참조하십시오.

필요한 권한 부여 수준은 파일을 업로드할지 다운로드할 것인지에 따라 다다.

파일을 다운로드하려는 경우 [저장소 Blob 데이터 판독기가](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) 사용자 ID, 관리 ID 또는 서비스 주체에 할당되었는지 확인합니다.

> 사용자 ID, 관리되는 ID 및 서비스 주체는 각각 *보안 주체의*유형이므로 이 문서의 나머지 부분에서 *보안 주체라는* 용어를 사용합니다.

파일을 업로드하려면 다음 역할 중 하나가 보안 주체에 할당되었는지 확인합니다.

- [Storage Blob 데이터 기여자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Storage Blob 데이터 소유자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

이러한 역할은 다음 범위 중 어느 범위에서든 보안 주체에게 할당될 수 있습니다.

- 컨테이너(파일 시스템)
- 스토리지 계정
- Resource group
- Subscription

역할을 확인하고 할당하는 방법을 알아보려면 [Azure 포털에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 권한 부여 액세스를](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)참조하세요.

> [!NOTE]
> RBAC 역할 할당이 전파되는 데 최대 5분이 걸릴 수 있습니다.

보안 주체가 대상 컨테이너 또는 디렉터리의 액세스 제어 목록(ACL)에 추가된 경우 보안 주체에 이러한 역할 중 하나가 할당될 필요가 없습니다. ACL에서 보안 주체는 대상 디렉터리에 대한 권한을 작성하고 컨테이너 및 각 상위 디렉터리에 대한 권한을 실행해야 합니다.

자세한 내용은 [Azure 데이터 레이크 저장소 Gen2의 액세스 제어를](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)참조하십시오.

#### <a name="authenticate-a-user-identity"></a>사용자 ID 인증

사용자 ID에 필요한 권한 부여 수준이 지정되었음을 확인한 후 명령 프롬프트를 열고 다음 명령을 입력한 다음 ENTER 키를 누릅니다.

```azcopy
azcopy login
```

두 개 이상의 조직에 속한 경우 저장소 계정이 속한 조직의 테넌트 ID를 포함합니다.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

`<tenant-id>` 자리 표시자를 저장소 계정이 속한 조직의 테넌트 ID로 바꿉습니다. 테넌트 ID를 찾으려면 Azure 포털에서 **Azure Active Directory > 속성 > 디렉터리 ID를** 선택합니다.

이 명령은 웹 사이트의 인증 코드와 URL을 반환합니다. 웹 사이트를 열고, 코드를 입력하고, **다음** 단추를 선택합니다.

![컨테이너 만들기](media/storage-use-azcopy-v10/azcopy-login.png)

로그인 창이 나타납니다. 이 창에서 Azure 계정 자격 증명을 사용하여 Azure 계정에 로그인합니다. 로그인에 성공하면 브라우저 창을 닫고 AzCopy를 사용할 수 있습니다.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>서비스 주체 인증

특히 온-프레미스를 실행할 때 사용자 상호 작용 없이 실행되는 스크립트 내에서 AzCopy를 사용하려는 경우 이 옵션을 사용하는 것이 좋습니다. Azure에서 실행되는 VM에서 AzCopy를 실행하려는 경우 관리되는 서비스 ID를 관리하기가 더 쉽습니다. 자세한 내용은 이 문서의 [관리되는 ID 인증](#managed-identity) 섹션을 참조하십시오.

스크립트를 실행하기 전에 서비스 주체의 자격 증명을 AzCopy에 제공할 수 있도록 대화식으로 적어도 한 번 로그인해야 합니다.  이러한 자격 증명은 보안 및 암호화된 파일에 저장되므로 스크립트에서 중요한 정보를 제공할 필요가 없습니다.

클라이언트 보안을 사용하거나 서비스 주체의 앱 등록과 연결된 인증서의 암호를 사용하여 계정에 로그인할 수 있습니다.

서비스 주체를 만드는 방법에 대해 자세히 알아보려면 포털사용 방법을 [참조하여 리소스에 액세스할 수 있는 Azure AD 응용 프로그램 및 서비스 주체를 만듭니다.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

일반적으로 서비스 주체에 대해 자세히 알아보려면 [Azure Active Directory의 응용 프로그램 및 서비스 주체 개체를](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) 참조하십시오.

##### <a name="using-a-client-secret"></a>클라이언트 보안 비밀 사용

먼저 `AZCOPY_SPA_CLIENT_SECRET` 서비스 주체의 앱 등록의 클라이언트 비밀로 환경 변수를 설정합니다.

> [!NOTE]
> 운영 체제의 환경 변수 설정이 아니라 명령 프롬프트에서 이 값을 설정해야 합니다. 이렇게 하면 값은 현재 세션에서만 사용할 수 있습니다.

이 예제에서는 PowerShell에서 이 작업을 수행하는 방법을 보여 주며 있습니다.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> 이 예제와 같이 프롬프트를 사용하는 것이 좋습니다. 이렇게 하면 본체의 명령 기록에 암호가 표시되지 않습니다.  

그런 다음 다음 명령을 입력한 다음 ENTER 키를 누릅니다.

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

`<application-id>` 자리 표시자를 서비스 주체의 앱 등록의 응용 프로그램 ID로 바꿉습니다. `<tenant-id>` 자리 표시자를 저장소 계정이 속한 조직의 테넌트 ID로 바꿉습니다. 테넌트 ID를 찾으려면 Azure 포털에서 **Azure Active Directory > 속성 > 디렉터리 ID를** 선택합니다. 

##### <a name="using-a-certificate"></a>인증서 사용

권한 부여를 위해 고유한 자격 증명을 사용하려는 경우 앱 등록에 인증서를 업로드한 다음 해당 인증서를 사용하여 로그인할 수 있습니다.

인증서를 앱 등록에 업로드하는 것 외에도 AzCopy가 실행될 컴퓨터 또는 VM에 인증서 사본을 저장해야 합니다. 이 인증서 복사본은 에 있어야 합니다. PFX 또는 . PEM 형식이며 개인 키를 포함해야 합니다. 개인 키는 암호로 보호되어야 합니다. Windows를 사용 중이고 인증서가 인증서 저장소에만 있는 경우 해당 인증서를 개인 키를 포함하여 PFX 파일로 내보내야 합니다. 지침은 [수출-Pfx인증서를](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps) 참조하십시오.

다음으로 환경 `AZCOPY_SPA_CERT_PASSWORD` 변수를 인증서 암호로 설정합니다.

> [!NOTE]
> 운영 체제의 환경 변수 설정이 아니라 명령 프롬프트에서 이 값을 설정해야 합니다. 이렇게 하면 값은 현재 세션에서만 사용할 수 있습니다.

이 예제에서는 PowerShell에서 이 작업을 수행하는 방법을 보여 주며 있습니다.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

그런 다음 다음 명령을 입력한 다음 ENTER 키를 누릅니다.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

`<path-to-certificate-file>` 자리 표시자를 인증서 파일에 대한 상대 적 또는 정규화된 경로로 바꿉습니다. AzCopy는 이 인증서에 대한 경로를 저장하지만 인증서 복사본을 저장하지 않으므로 해당 인증서를 제자리에 유지해야 합니다. `<tenant-id>` 자리 표시자를 저장소 계정이 속한 조직의 테넌트 ID로 바꿉습니다. 테넌트 ID를 찾으려면 Azure 포털에서 **Azure Active Directory > 속성 > 디렉터리 ID를** 선택합니다.

> [!NOTE]
> 이 예제와 같이 프롬프트를 사용하는 것이 좋습니다. 이렇게 하면 본체의 명령 기록에 암호가 표시되지 않습니다. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>관리되는 ID 인증

사용자 상호 작용 없이 실행 되는 스크립트 내에서 AzCopy를 사용 하려는 경우이 옵션을 사용 하 여 Azure 가상 컴퓨터 (VM)에서 실행 됩니다. 이 옵션을 사용하는 경우 VM에 자격 증명을 저장할 필요가 없습니다.

VM에서 활성화한 시스템 전체 관리 ID를 사용하거나 VM에 할당한 사용자 할당된 관리 ID의 클라이언트 ID, 개체 ID 또는 리소스 ID를 사용하여 계정에 로그인할 수 있습니다.

시스템 전체에서 관리되는 ID를 사용하도록 설정하거나 사용자가 할당한 관리되는 ID를 만드는 방법에 대해 자세히 알아보려면 [Azure 포털을 사용하여 VM에서 Azure 리소스에 대한 관리ID 구성 을](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)참조하십시오.

##### <a name="using-a-system-wide-managed-identity"></a>시스템 전체관리 ID 사용

먼저 VM에서 시스템 전체의 관리ID를 사용하도록 설정했는지 확인합니다. [시스템 할당 관리 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)를 참조하십시오.

그런 다음 명령 콘솔에서 다음 명령을 입력한 다음 ENTER 키를 누릅니다.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>사용자 할당된 관리되는 ID 사용

먼저 VM에서 사용자가 할당한 관리 ID를 사용하도록 설정했는지 확인합니다. [사용자 할당된 관리되는 ID를](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity)참조하십시오.

그런 다음 명령 콘솔에서 다음 명령 중 아무 것만 입력한 다음 ENTER 키를 누릅니다.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

자리 `<client-id>` 표시자를 사용자 지정 관리 ID의 클라이언트 ID로 바꿉습니다.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

자리 `<object-id>` 표시자를 사용자가 할당한 관리 ID의 개체 ID로 바꿉습니다.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

자리 `<resource-id>` 표시자를 사용자가 할당한 관리 ID의 리소스 ID로 바꿉습니다.

### <a name="option-2-use-a-sas-token"></a>옵션 2: SAS 토큰 사용

AzCopy 명령에서 사용하는 각 소스 또는 대상 URL에 SAS 토큰을 추가할 수 있습니다.

이 예제 명령은 로컬 디렉터리에서 Blob 컨테이너로 데이터를 재귀적으로 복사합니다. 가상의 SAS 토큰은 컨테이너 URL의 끝에 추가됩니다.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

SAS 토큰 및 토큰을 얻는 방법에 대한 자세한 내용은 [SAS(공유 액세스 서명 사용)를](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)참조하십시오.

## <a name="transfer-files"></a>파일 전송

ID를 인증했거나 SAS 토큰을 얻은 후 파일 전송을 시작할 수 있습니다.

예제 명령을 찾으려면 다음 문서 중 일부를 참조하십시오.

- [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](storage-use-azcopy-blobs.md)

- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)

- [AzCopy 및 Amazon S3 버킷을 사용하여 데이터 전송](storage-use-azcopy-s3.md)

- [AzCopy 및 Azure 스택 저장소로 데이터 전송](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>스크립트에서 AzCopy 사용

### <a name="obtain-a-static-download-link"></a>정적 다운로드 링크 얻기

시간이 지남에 따라 AzCopy [다운로드 링크는](#download-and-install-azcopy) AzCopy의 새 버전을 가리킵니다. 스크립트가 AzCopy를 다운로드하는 경우 최신 버전의 AzCopy가 스크립트에 종속된 기능을 수정하는 경우 스크립트가 작동하지 않을 수 있습니다.

이러한 문제를 방지하려면 현재 버전의 AzCopy에 대한 정적(변경되지 않은) 링크를 가져옵니다. 이렇게 하면 스크립트가 실행될 때마다 동일한 버전의 AzCopy를 다운로드합니다.

링크를 얻으려면 다음 명령을 실행합니다.

| 운영 체제  | 명령 |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Linux의 `--strip-components=1` 경우 `tar` 명령에서 버전 이름이 포함된 최상위 폴더를 제거하고 대신 바이너리를 현재 폴더로 직접 추출합니다. 이렇게 하면 URL만 `azcopy` 업데이트하여 새 버전으로 스크립트를 `wget` 업데이트할 수 있습니다.

URL이 이 명령의 출력에 나타납니다. 그런 다음 스크립트는 해당 URL을 사용하여 AzCopy를 다운로드할 수 있습니다.

| 운영 체제  | 명령 |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>SAS 토큰에서 특수 문자 탈출

`.cmd` 확장명이 있는 일괄 처리 파일에서는 SAS `%` 토큰에 나타나는 문자를 이스케이프해야 합니다. SAS 토큰 문자열의 `%` 기존 `%` 문자 옆에 추가 문자를 추가하여 이 작업을 수행할 수 있습니다.

### <a name="run-scripts-by-using-jenkins"></a>젠킨스를 사용하여 스크립트 를 실행

[Jenkins를](https://jenkins.io/) 사용하여 스크립트를 실행하려는 경우 스크립트 시작 부분에 다음 명령을 배치해야 합니다.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>Azure 저장소 탐색기에서 AzCopy 사용

[저장소 탐색기는](https://azure.microsoft.com/features/storage-explorer/) AzCopy를 사용하여 모든 데이터 전송 작업을 수행합니다. AzCopy의 성능 이점을 활용하려는 경우 [저장소 탐색기를](https://azure.microsoft.com/features/storage-explorer/) 사용할 수 있지만 명령줄 대신 그래픽 사용자 인터페이스를 사용하여 파일과 상호 작용하는 것을 선호합니다.

저장소 탐색기작업을 수행하기 위해 계정 키를 사용하므로 저장소 탐색기에 로그인한 후에는 추가 권한 부여 자격 증명을 제공할 필요가 없습니다.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>이전 버전의 AzCopy 사용

이전 버전의 AzCopy를 사용해야 하는 경우 다음 링크 중 하나를 참조하십시오.

- [Windows에서 AzCopy(v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [리눅스에 AzCopy (v7)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy 구성, 최적화 및 문제 해결

[AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md) 을 참조하십시오.

## <a name="next-steps"></a>다음 단계

질문, 문제 또는 일반적인 피드백이 있는 경우 [GitHub 페이지에](https://github.com/Azure/azure-storage-azcopy) 제출합니다.
