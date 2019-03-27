---
title: V10 AzCopy를 사용 하 여 Azure Storage에 데이터를 이동 또는 복사 (미리 보기) | Microsoft Docs
description: AzCopy v10를 사용 하 여 이동 하거나 blob에서 data lake 및 파일 콘텐츠에서 데이터를 복사 하려면 명령줄 유틸리티 (미리 보기). 로컬 파일에서 Azure Storage로 데이터를 복사하거나, Storage 계정 내에서 데이터를 복사하거나, Storage 계정 간에 데이터를 복사합니다. 데이터를 Azure Storage로 손쉽게 마이그레이션할 수 있습니다.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 02/24/2019
ms.author: artemuwka
ms.subservice: common
ms.openlocfilehash: ffc4a0c57681e877250c7be82f5160174178892a
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486022"
---
# <a name="transfer-data-with-azcopy-v10-preview"></a>V10 AzCopy 사용 하 여 데이터 전송 (미리 보기)

AzCopy v10 (미리 보기)는 Microsoft Azure Blob 및 File storage 간에 데이터를 복사 하기 위한 명령줄 유틸리티입니다. 신뢰할 수 있는 데이터에 대 한 새로운 아키텍처를 전송 및 AzCopy v10 다시 디자인 된 명령줄 인터페이스를 제공 합니다. AzCopy를 사용 하 여 파일 시스템 및 저장소 계정 간에 또는 storage 계정 간에 데이터를 복사할 수 있습니다.

## <a name="whats-new-in-azcopy-v10"></a>AzCopy v10의 새로운 기능

- Azure Blob storage로 또는 그 반대로 파일 시스템을 동기화합니다. `azcopy sync <source> <destination>`를 사용합니다. 증분 복사 시나리오에 적합합니다.
- Azure Data Lake Storage Gen2 API를 지원합니다. 사용 하 여 `myaccount.dfs.core.windows.net` Data Lake 저장소 Gen2 Api를 호출 하는 URI로 합니다.
- 계정 전체를 다른 계정으로 복사하는 기능을 지원합니다(Blob 서비스만 해당).
- 새로운 [URL에서 블록 배치](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) Api 계정에 복사를 지원 하도록 합니다. 데이터 전송이 빠르고 이므로 클라이언트에 전송에는 필요 하지 않습니다.입니다.
- 나열 하거나 지정된 된 경로에서 파일 및 blob을 제거 합니다.
- 경로 및--제외 플래그의 와일드 카드 패턴을 지원합니다.
- 모든 AzCopy 인스턴스를 사용 하 여 작업 순서와 관련 된 로그 파일을 만듭니다. 보기 및 이전 작업을 다시 시작 하 고 실패 한 작업을 다시 시작할 수 있습니다. AzCopy는 전송 실패 후 자동으로 전송을 다시 시도합니다.
- 기능 일반 성능 향상입니다.

## <a name="download-and-install-azcopy"></a>AzCopy 다운로드 및 설치

### <a name="latest-preview-version-v10"></a>최신 미리 보기 버전(v10)

AzCopy의 최신 미리 보기 버전을 다운로드합니다.
- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

### <a name="latest-production-version-v81"></a>최신 프로덕션 버전(v8.1)

[Windows용 AzCopy의 최신 프로덕션 버전](https://aka.ms/downloadazcopy)을 다운로드합니다.

### <a name="azcopy-supporting-table-storage-service-v73"></a>Table Storage 서비스(v7.3)를 지원하는 AzCopy

[Microsoft Azure Table Storage 서비스와 데이터 복사를 지원하는 AzCopy v7.3](https://aka.ms/downloadazcopynet)을 다운로드합니다.

## <a name="post-installation-steps"></a>설치 후 단계

AzCopy v10 설치가 필요 하지 않습니다. 선호 하는 명령줄 응용 프로그램을 열고 폴더를 찾은 위치 `azcopy.exe` 위치한 합니다. 필요한 경우에 사용 편의성을 위해 시스템 경로 AzCopy 폴더 위치를 추가할 수 있습니다.

## <a name="authentication-options"></a>인증 옵션

AzCopy v10에서는 Azure Storage를 사용 하 여 인증 하는 경우 다음 옵션을 지원 합니다.
- **Azure Active Directory** (지원 **Blob 및 Data Lake 저장소 Gen2 서비스**). 사용 하 여 ```.\azcopy login``` Azure Active Directory에 로그인 합니다.  사용자에 게 ["Storage Blob 데이터 기여자" 역할이 할당](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) Azure Active Directory 인증을 사용 하 여 Blob storage에 쓸 수 있습니다. Azure 리소스에 대 한 관리 되는 id 통해 인증을 사용 하 여 `azcopy login --identity`입니다.
- **공유 액세스 서명 토큰 [Blob 및 파일 서비스에 대 한 지원 되는]** 합니다. 공유 액세스 서명 (SAS) 토큰을 사용 하려면 명령줄에서 blob 경로에 추가 합니다. Azure portal 사용 하 여 SAS 토큰을 생성할 수 있습니다 [Storage 탐색기](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken), 또는 기타 도구를 선택 합니다. 자세한 내용은 [예제](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)를 참조하세요.

## <a name="getting-started"></a>시작

> [!TIP]
> **그래픽 사용자 인터페이스를 선호 하십니까?**
>
> [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/), Azure Storage 데이터 관리를 간소화 하는 데스크톱 클라이언트를 이제 가속화와 Azure Storage에서 데이터 전송에 AzCopy를 사용 합니다.
>
> Storage 탐색기에서 AzCopy를 사용 하도록 설정 합니다 **미리 보기** 메뉴.
> ![Azure Storage 탐색기에서 전송 엔진으로 AzCopy를 사용 하도록 설정](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

AzCopy v10 자체 문서화 된 구문이 있습니다. Azure Active Directory에 로그인 하는 경우 일반 구문은 다음과 같습니다.

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>

# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you're using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

다음과 같은 방법으로 사용 가능한 명령 목록을 가져올 수 있습니다.

```azcopy
.\azcopy --help
# To use the alias instead
.\azcopy -h
```

도움말 페이지 및 특정 명령에 대 한 예제를 보려면 다음 명령을 실행 합니다.

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Blob 컨테이너 또는 파일 공유 만들기 

**Blob 컨테이너 만들기**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**파일 공유 만들기**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Azure Data Lake 저장소 Gen2를 사용 하 여 blob 컨테이너 만들기**

Blob 저장소 계정에서 계층적 네임 스페이스를 사용 하도록 설정 하는 경우 파일을 업로드 하는 것에 대 한 새 blob 컨테이너를 만들려면 다음 명령을 사용할 수 있습니다.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Azure Storage에 데이터 복사

복사 명령을 사용하여 원본에서 대상으로 데이터를 전송합니다. 소스 또는 대상에는 a: 수 있습니다.
- 로컬 파일 시스템
- Azure Blob/가상 디렉터리/컨테이너 URI
- Azure 파일/디렉터리/파일 공유 URI
- Azure Data Lake Storage Gen2 파일 시스템/디렉터리/파일 URI

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using the alias instead 
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

폴더 아래에 있는 모든 파일을 업로드 하는 명령을 `C:\local\path` 컨테이너에 재귀적으로 `mycontainer1`만들기, `path` 컨테이너에 디렉터리:

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

다음 명령은 `C:\local\path` 폴더의 모든 파일을 `mycontainer1` 컨테이너에 업로드합니다(하위 디렉터리로 반복하지 않고).

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

더 많은 예제를 찾으려면 다음 명령을 사용 합니다.

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>두 저장소 계정 간에 데이터 복사

사용 하 여 두 개의 저장소 계정 간에 데이터를 복사 합니다 [URL에서 블록 배치](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API를 클라이언트 컴퓨터의 네트워크 대역폭을 사용 하지 않습니다. AzCopy는 단순히 복사 작업을 오케스트레이션 하는 동안 데이터가 두 Azure 저장소 서버를 직접 복사 됩니다. 이 옵션은 현재 Blob 저장소에 사용할 수만 있습니다.

두 저장소 계정 간에 데이터를 복사하려면 다음 명령을 사용합니다.
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> 이 명령은 모든 blob 컨테이너를 열거 하 고 대상 계정으로 복사 됩니다. 이때 AzCopy v10 두 저장소 계정 간에 블록 blob만 복사를 지원 합니다. 모든 다른 저장소 계정 개체 (예: blob, 페이지 blob, 파일, 테이블 및 큐 추가) 건너뜁니다.

## <a name="copy-a-vhd-image-to-a-storage-account"></a>VHD 이미지를 저장소 계정에 복사

AzCopy v10은 기본적으로 블록 Blob에 데이터를 업로드합니다. 그러나 소스 파일에 있는 경우는 `.vhd` 확장 AzCopy v10 기본적으로 페이지 blob에 업로드 합니다. 지금은이 작업은 구성할 수 없습니다.

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>동기화: 증분 복사 및 삭제(Blob 스토리지만)

동기화 명령 파일 이름을 비교 대상에서 디렉터리로 원본 디렉터리의 콘텐츠를 동기화 및 타임 스탬프를 마지막으로 수정 합니다. 원본에 존재 하지 않는 경우이 작업 대상 파일의 선택적 삭제를 포함 하면는 `--delete-destination=prompt|true` 플래그가 제공 된 합니다. 기본적으로 delete 동작을 사용 하는 사용할 수 없습니다. 

> [!NOTE] 
> 사용 된 `--delete-destination` 주의 해 서 플래그입니다. 사용 하도록 설정 합니다 [일시 삭제](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) 삭제 동작 계정의 실수로 인 한 삭제를 방지 하려면 동기화를 활성화 하기 전에 기능입니다. 

> 때 `--delete-destination` 로 설정 된 true, AzCopy는 파일을 삭제 사용자에 게 프롬프트 없이 대상에서 원본에 존재 하지 않습니다. 확인에 대 한 자격 증명을 입력 하려는 경우 사용할 `--delete-destination=prompt`합니다.

로컬 파일 시스템을 저장소 계정과 동기화하려면 다음 명령을 사용합니다.

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

로컬 파일 시스템으로 blob 컨테이너를 동기화 할 수도 있습니다.

```azcopy
# The SAS token isn't required for Azure Active Directory authentication.
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

이 명령은 마지막으로 수정 된 타임 스탬프에 따라 대상에 소스를 증분 방식으로 동기화 합니다. 원본에서 파일이 추가 또는 삭제되면 AzCopy v10이 대상에서 똑같은 작업을 수행합니다. 삭제 하기 전에 AzCopy 것인지 묻습니다.

## <a name="advanced-configuration"></a>고급 구성

### <a name="configure-proxy-settings"></a>프록시 설정 구성

AzCopy v10에 대 한 프록시 설정을 구성 하려면 다음 명령을 사용 하 여 환경 변수 https_proxy를 설정 합니다.

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>처리량 최적화

AZCOPY_CONCURRENCY_VALUE 동시 요청 수를 구성 하 고 처리량 성능 및 리소스 사용량을 제어 하는 환경 변수를 설정 합니다. 이 값은 기본적으로 300으로 설정됩니다. 값을 줄이면 AzCopy v10에서 사용하는 대역폭 및 CPU가 제한됩니다.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank then the default value is currently in use
```

### <a name="change-the-location-of-the-log-files"></a>로그 파일의 위치 변경

필요에 따라 또는 OS 디스크가 채워지는 것을 방지하기 위해 로그 파일의 위치를 변경할 수 있습니다.

```cmd
# For Windows:
set AZCOPY_LOG_LOCATION=<value>
# For Linux:
export AZCOPY_LOG_LOCATION=<value>
# For MacOS
export AZCOPY_LOG_LOCATION=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank, then the default value is currently in use
```
### <a name="change-the-default-log-level"></a>기본 로그 수준 변경 

기본적으로 AzCopy 로그 수준이 정보에 설정 됩니다. 디스크 공간 절약을 위해 로그의 세부 정보 표시를 줄이려는 경우 ``--log-level`` 옵션을 사용하여 설정을 덮어씁니다. 사용 가능한 로그 수준은 다음과 같습니다. 디버그, 정보, 경고, 오류, 서둘러, 및 치명적입니다.

### <a name="review-the-logs-for-errors"></a>오류에 대한 로그 검토

다음 명령은 04dc9ca9-158f-7945-5933-564021086c79 로그에서 상태가 UPLOADFAILED인 모든 오류를 가져옵니다.

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```
## <a name="troubleshooting"></a>문제 해결

AzCopy v10까지 모든 작업에 대 한 계획 파일과 로그 파일을 만듭니다. 로그를 사용하여 잠재적 문제를 조사하고 해결할 수 있습니다. 로그에는 실패 상태(UPLOADFAILED, COPYFAILED 및 DOWNLOADFAILED), 전체 경로, 실패 이유가 포함됩니다. 작업 로그 및 플랜 파일은 Windows의 %USERPROFILE\\.azcopy 폴더 또는 Mac 및 Linux의 $HOME\\.azcopy 폴더에 있습니다.

> [!IMPORTANT]
> Microsoft 지원 (또는 제 3 자에 관련 된 문제 해결)에 요청을 제출 하는 경우 실행 하려는 명령의 교정된 버전을 공유 합니다. 그러면 SAS가 모든 사용자와 실수로 공유 되지 않습니다. 수정 버전은 로그 파일의 시작 부분에서 찾을 수 있습니다.

### <a name="view-and-resume-jobs"></a>작업 보기 및 다시 시작

각 전송 작업은 AzCopy 작업을 만듭니다. 작업의 기록을 보려면 다음 명령을 사용 합니다.

```azcopy
.\azcopy jobs list
```

작업 상태를 보려면 다음 명령을 사용합니다.

```azcopy
.\azcopy jobs show <job-id>
```

전송을 상태별로 필터링하려면 다음 명령을 사용합니다.

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

다음 명령을 사용 하 여 실패/취소 작업을 다시 시작 합니다. 이 명령은 SAS 토큰과 함께 해당 id를 사용합니다. 보안상의 이유로 영구 없습니다.

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

## <a name="next-steps"></a>다음 단계

질문, 문제 또는 일반적인 피드백 경우 제출해 [GitHub에서](https://github.com/Azure/azure-storage-azcopy)합니다.


