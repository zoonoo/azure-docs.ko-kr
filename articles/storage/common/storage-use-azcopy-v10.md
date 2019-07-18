---
title: V10 AzCopy를 사용 하 여 Azure Storage에 데이터를 이동 또는 복사 | Microsoft Docs
description: AzCopy는, 또는 저장소 계정 간에 데이터를 복사 하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에서는 AzCopy를 다운로드, 저장소 계정에 연결한 후 다음 파일을 전송 합니다.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 94aca33b2f12c1c39297221a856296dcca052b0f
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565792"
---
# <a name="get-started-with-azcopy"></a>AzCopy 시작

AzCopy는 저장소 계정 간에서 blob 또는 파일을 복사 하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에서는 AzCopy를 다운로드, 저장소 계정에 연결한 후 다음 파일을 전송 합니다.

> [!NOTE]
> AzCopy **V10** AzCopy의 현재 지원 되는 버전입니다.
>
> AzCopy를 사용 해야 하는 경우 **v8.1**를 참조 합니다 [이전 버전의 AzCopy 사용 하 여](#previous-version) 이 문서의 섹션입니다.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>AzCopy 다운로드

먼저, 컴퓨터에 디렉터리를 AzCopy V10 실행 파일을 다운로드 합니다. 

- [Windows](https://aka.ms/downloadazcopy-v10-windows)(zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux)(tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac)(zip)

> [!NOTE]
> 데이터를 복사 하려는 경우에 [Azure Table storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) 서비스를 설치 [AzCopy 버전 7.3](https://aka.ms/downloadazcopynet)합니다.

## <a name="run-azcopy"></a>AzCopy를 실행 합니다.

편의 위해 쉽게 사용할 수 있도록 시스템 경로 AzCopy 실행 파일의 디렉터리 위치를 추가 하는 것이 좋습니다. 이런 방식으로 입력할 수 있습니다 `azcopy` 시스템에서 모든 디렉터리에서.

입력 하 여 AzCopy 실행 파일의 위치로 디렉터리를 변경 해야 AzCopy 디렉터리 경로를 추가 하지 않으려는 경우 `azcopy` 또는 `.\azcopy` Windows PowerShell 명령 프롬프트에서.

명령 목록을 보려면 `azcopy -h` 한 다음 ENTER 키를 누릅니다.

특정 명령에 대 한 자세한 포함 된 명령의 이름입니다 (예: `azcopy list -h`).

![인라인 도움말](media/storage-use-azcopy-v10/azcopy-inline-help.png)

> [!NOTE] 
> Azure Storage 계정의 소유자는 데이터에 액세스 권한은 자동으로 할당 되지 있습니다. AzCopy를 사용 하 여 의미 있는 모든 작업을 수행 하려면, 저장소 서비스에 권한 부여 자격 증명 제공할 방법을 결정 해야 합니다. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>권한 부여 자격 증명 제공할 방법 선택

Azure AD (Active Directory)를 사용 하거나 공유 액세스 서명 (SAS) 토큰을 사용 하 여 권한 부여 자격 증명을 제공할 수 있습니다.

이 테이블을 가이드로 사용 합니다.

| 저장소 유형 | 권한 부여의 현재 지원 되는 메서드 |
|--|--|
|**Blob Storage** | Azure AD & SAS |
|**Blob 저장소 (계층적 네임 스페이스)** | Azure AD & SAS |
|**File Storage** | SAS만 |

### <a name="option-1-use-azure-ad"></a>옵션 1: Azure AD를 사용 합니다.

Azure AD를 사용 하 여 각 명령에 SAS 토큰을 추가할 필요 없이 한 번 자격 증명을 제공할 수 있습니다.  

해야 하는 권한 부여 수준을 기반으로 파일 업로드 또는 다운로드만 것인지 합니다.

파일을 다운로드 하려는 경우 확인 합니다 [Storage Blob 데이터 판독기](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) 사용자 id 또는 서비스 주체에 할당 된 합니다. 

> [!NOTE]
> 사용자 id 및 서비스 주체는 각 유형의 *보안 주체*용어 사용 되므로 *보안 주체* 이 문서의 나머지 부분에 대 한 합니다.

파일 업로드 하려는 경우에 이러한 역할 중 하나 보안 주체에 할당 되었는지 확인 한 다음:

- [Storage Blob 데이터 기여자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Storage Blob 데이터 소유자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

이러한 범위에 있는 id에 이러한 역할을 할당할 수 있습니다.

- 컨테이너 (파일 시스템)
- Storage 계정
- 리소스 그룹
- 구독

참조를 확인 하 고 역할을 할당 하는 방법을 알아보려면 [Azure blob 및 큐 데이터에 RBAC 사용 하 여 Azure portal에서 액세스 권한을 부여](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)합니다.

> [!NOTE] 
> RBAC 역할 할당이 전파 하는 데 최대 5 분을 사용할 수 있는 점을 염두에 두십시오.

보안 주체에 대상 컨테이너 또는 디렉터리의 액세스 제어 목록 (ACL)에 추가 되 면 보안 주체에 할당 된 이러한 역할 중 하나를 사용할 필요가 없습니다. ACL을 보안 주체에 대 한 대상 디렉터리에 쓰기 및 컨테이너와 각 부모 디렉터리에 대 한 실행 해야 합니다.

자세한 내용은 참조 하세요 [Azure Data Lake 저장소 Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)입니다.

#### <a name="authenticate-a-user-identity"></a>사용자 id를 인증 합니다.

사용자 id에 필요한 권한 수준을 부여 된를 확인 한 후 명령 프롬프트를 열고, 다음 명령을 입력 하 고 ENTER 키를 누릅니다.

```azcopy
azcopy login
```

둘 이상의 조직에 속하는 경우 저장소 계정이 속해 있는 조직의 테 넌 트 ID를 포함 합니다.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

대체는 `<tenant-id>` 저장소 계정이 속해 있는 조직의 테 넌 트 ID 사용 하 여 자리 표시자입니다. 테 넌 트 ID를 찾으려면 선택 **Azure Active Directory > 속성 > 디렉터리 ID** Azure portal에서 합니다.

이 명령은 웹 사이트의 인증 코드와 URL을 반환합니다. 웹 사이트를 열고, 코드를 입력하고, **다음** 단추를 선택합니다.

![컨테이너 만들기](media/storage-use-azcopy-v10/azcopy-login.png)

로그인 창이 나타납니다. 이 창에서 Azure 계정 자격 증명을 사용하여 Azure 계정에 로그인합니다. 로그인에 성공하면 브라우저 창을 닫고 AzCopy를 사용할 수 있습니다.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>서비스 주체 인증

사용자 개입 없이 실행 되는 스크립트 내에서 AzCopy를 사용 하려는 경우 유용한 옵션입니다. 

해당 스크립트를 실행 하기 전에 로그인에 대화형으로 최소 한 번 이상 서비스 주체의 자격 증명을 사용 하 여 AzCopy를 제공할 수 있도록 해야 합니다.  스크립트는 중요 한 정보를 제공 하지 않아도 되도록 해당 자격 증명을 안전 하 고 암호화 된 파일에 저장 됩니다.

클라이언트 암호를 사용 하 여 또는 서비스 주체의 앱 등록을 사용 하 여 연결 된 인증서의 암호를 사용 하 여 계정에 서명할 수 있습니다. 

서비스 주체 만들기에 대 한 자세한 내용은를 참조 하세요. [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

참조에 대해 자세히 알아보려면 서비스 주체 일반적 [응용 프로그램 및 Azure Active Directory에서 서비스 주체 개체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>클라이언트 암호를 사용 하 여

설정 하 여 시작 된 `AZCOPY_SPA_CLIENT_SECRET` 환경 변수 서비스 주체의 클라이언트 암호를 앱 등록의 합니다. 

> [!NOTE]
> 이 값을 설정할 환경 및 명령 프롬프트에서 운영 체제의 변수 설정 해야 합니다. 이런 방식으로 값은 현재 세션에만 사용할 수 있습니다.

이 예제에서는 PowerShell에서이 수행할 수는 방법을 보여 줍니다.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> 이 예제에 표시 된 대로 프롬프트를 사용 하는 것이 좋습니다. 이렇게 하면 클라이언트 비밀 콘솔의 명령 기록에 나타나지 않습니다. 

다음으로, 다음 명령을 입력 하 고 ENTER 키를 누릅니다.

```azcopy
azcopy login --service-principal --application-id <application-id>
```

대체는 `<application-id>` 서비스 주체의 응용 프로그램 ID 사용 하 여 자리 표시자의 앱 등록 합니다.

##### <a name="using-a-certificate"></a>인증서 사용

권한 부여에 대 한 사용자 고유의 자격 증명을 사용 하려는 경우 앱 등록 인증서를 업로드할 수 있으며 다음 로그인에 해당 인증서를 사용할 수 있습니다.

인증서를 업로드 하 여 앱 등록을 하는 것 외에도 컴퓨터 또는 AzCopy를 실행 하는 VM에 저장 된 인증서의 복사본이 해야 있습니다. 인증서의이 복사본에 있어야 합니다. PFX 또는 합니다. PEM 서식을 지정 하 고 개인 키를 포함 해야 합니다. 개인 키에는 암호 보호를 사용 해야 합니다. Windows를 사용 하는 인증서를 인증서 저장소에만 존재 하는 경우 해당 인증서 (개인 키 포함)를 PFX 파일로 내보내려면 있는지 확인 합니다. 지침은 [Export-pfxcertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

다음으로 설정 된 `AZCOPY_SPA_CERT_PASSWORD` 인증서 암호 환경 변수입니다.

> [!NOTE]
> 이 값을 설정할 환경 및 명령 프롬프트에서 운영 체제의 변수 설정 해야 합니다. 이런 방식으로 값은 현재 세션에만 사용할 수 있습니다.

이 예제에서는 PowerShell에서이 수행할 수는 방법을 보여 줍니다.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

다음으로, 다음 명령을 입력 하 고 ENTER 키를 누릅니다.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file>
```

대체는 `<path-to-certificate-file>` 인증서 파일의 상대 또는 정규화 된 경로 사용 하 여 자리 표시자입니다. AzCopy는 경로이 인증서를 저장 하지만 해당 인증서 장소에 보관 해야 합니다는 인증서의 복사본을 저장 하지 않습니다.

> [!NOTE]
> 이 예제에 표시 된 대로 프롬프트를 사용 하는 것이 좋습니다. 이런 방식으로 암호 콘솔의 명령 기록에 나타나지 않습니다. 

### <a name="option-2-use-a-sas-token"></a>옵션 2: SAS 토큰 사용

AzCopy 명령에서는 SAS 토큰을 사용 하는 각 원본 또는 대상 URL에 추가할 수 있습니다.

이 예제에서는 명령을 재귀적으로 blob 컨테이너에 로컬 디렉터리에서 데이터를 복사합니다. 가상의 SAS 토큰의 끝에 추가 되는 컨테이너 url입니다.

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

SAS 토큰 및 구입 하는 방법에 대 한 자세한 내용은 참조 하세요 [공유 액세스 서명 (SAS) 사용](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)합니다.

## <a name="transfer-files"></a>파일 전송

사용자 본인을 인증 하거나 SAS 토큰을 후 파일 전송을 시작할 수 있습니다.

예제 명령은 찾기 위해 이러한 문서 중 하나를 참조 하세요.

- [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](storage-use-azcopy-blobs.md)

- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)

- [AzCopy 및 Amazon S3 버킷을 사용하여 데이터 전송](storage-use-azcopy-s3.md)

- [AzCopy 및 Azure Stack 저장소를 사용 하 여 데이터를 전송 합니다.](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>AzCopy를 사용 하 여 스크립트에서

해당 스크립트를 실행 하기 전에 로그인에 대화형으로 최소 한 번 이상 서비스 주체의 자격 증명을 사용 하 여 AzCopy를 제공할 수 있도록 해야 합니다.  스크립트는 중요 한 정보를 제공 하지 않아도 되도록 해당 자격 증명을 안전 하 고 암호화 된 파일에 저장 됩니다. 예제를 보려면 합니다 [서비스 주체 인증](#service-principal) 이 문서의 섹션입니다.

AzCopy 시간별 [다운로드 링크](#download-and-install-azcopy) 새 버전의 AzCopy 가리키게 됩니다. 스크립트 AzCopy를 다운로드, 최신 버전의 AzCopy 스크립트 종속 된 기능을 수정 하는 경우 작업 스크립트를 중지할 수 있습니다. 

이러한 문제를 방지 하려면 현재 버전의 AzCopy에 정적 (변경 되지 않은)에 대 한 링크를 가져옵니다. 이런 방식으로 스크립트 다운로드 정확히 동일한 버전의 AzCopy 실행 되는 각 시간입니다.

링크를 가져오려면이 명령을 실행 합니다.

| 운영 체제  | 명령 |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Linux의 경우 `--strip-components=1` 에 `tar` 명령은 버전 이름을 포함 하 고 대신 현재 폴더에 직접 이진 파일을 추출 하는 최상위 폴더를 제거 합니다. 이렇게 하면 새 버전의 업데이트 스크립트 `azcopy` 만 업데이트 하 여는 `wget` URL입니다.

URL이 명령의 출력에 표시 됩니다. 다음 스크립트는 URL을 사용 하 여 AzCopy를 다운로드할 수 있습니다.

| 운영 체제  | 명령 |
|--------|-----------|
| **Linux** | `wget -O azcopyv10.tar https://azcopyvnext.azureedge.net/release20190301/azcopy_linux_amd64_10.0.8.tar.gz tar -xf azcopyv10.tar --strip-components=1 ./azcopy` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

## <a name="use-azcopy-in-storage-explorer"></a>AzCopy를 사용 하 여 Storage 탐색기에서

AzCopy의 성능 이점을 활용 하 여 파일을 사용 하 여 상호 작용 하도록 명령줄 보다는 Storage 탐색기를 사용 하려는 경우, Storage 탐색기에서 AzCopy를 사용 합니다. 

Storage 탐색기에서 선택 **미리 보기**->**향상 된 Blob 업로드 및 다운로드에 사용 하 여 AzCopy**합니다.

![Azure Storage 탐색기에서 전송 엔진으로 AzCopy를 사용 하도록 설정](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> 저장소 계정에서 계층적 네임 스페이스를 사용 하도록 설정 하는 경우이 설정을 사용 하도록 설정 필요가 없습니다. 계층적 네임 스페이스를 포함 하는 저장소 계정에서 Storage 탐색기 자동으로 사용 하 여 AzCopy 때문입니다.  

저장소 탐색기 계정 키를 사용 하 여 작업을 수행 하므로 저장소 탐색기에 로그인 한 후 추가 권한 부여 자격 증명을 제공할 필요가 없습니다.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>이전 버전의 AzCopy 사용 합니다.

이전 버전의 AzCopy (AzCopy v8.1)를 사용 하는 경우 다음 링크 중 하나를 참조 하세요.

- [(V8) Windows에서 AzCopy](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [(V8) Linux에서 AzCopy](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>구성, 최적화 및 AzCopy 문제 해결

참조 [구성 최적화 및 AzCopy 문제 해결](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>다음 단계

질문, 문제 또는 일반적인 피드백 경우 제출해 [GitHub에서](https://github.com/Azure/azure-storage-azcopy) 페이지입니다.
