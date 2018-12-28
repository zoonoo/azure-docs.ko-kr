---
title: AzCopy v10(미리 보기)을 사용하여 Azure Storage로 데이터 복사 또는 이동 | Microsoft Docs
description: AzCopy v10(미리 보기) 유틸리티를 사용하여 Blob, 테이블 및 파일 콘텐츠에서 데이터를 이동하거나 복사합니다. 로컬 파일에서 Azure Storage로 데이터를 복사하거나, Storage 계정 내에서 데이터를 복사하거나, Storage 계정 간에 데이터를 복사합니다. 데이터를 Azure Storage로 손쉽게 마이그레이션할 수 있습니다.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 10/09/2018
ms.author: artemuwka
ms.component: common
ms.openlocfilehash: 2ab933506ea03ae72198113d70888460e5001a6d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52958425"
---
# <a name="transfer-data-with-the-azcopy-v10-preview"></a>AzCopy v10(미리 보기)을 사용하여 데이터 전송

AzCopy v10(미리 보기)은 Microsoft Azure Blob 및 파일 저장소와 데이터를 복사하는 데 사용되는 차세대 명령줄 유틸리티로, 안정적인 고성능 데이터 전송을 위해 다시 디자인된 명령줄 인터페이스와 새 아키텍처를 제공합니다. AzCopy를 사용하여 파일 시스템과 저장소 계정 간 또는 저장소 계정 간에 데이터를 복사할 수 있습니다.

## <a name="whats-new-in-azcopy-v10"></a>AzCopy v10의 새로운 기능

- 파일 시스템을 Azure Blob에 또는 그 반대로 동기화합니다. `azcopy sync <source> <destination>`를 사용합니다. 증분 복사 시나리오에 적합합니다.
- Azure Data Lake Storage Gen2 API를 지원합니다. ADLS Gen2 API를 호출하는 URI로 `myaccount.dfs.core.windows.net`을 사용하세요.
- 계정 전체를 다른 계정으로 복사하는 기능을 지원합니다(Blob 서비스만 해당).
- 현재 계정 간 복사에는 새로운 [Put from URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API가 사용됩니다. 클라이언트에 데이터를 전송할 필요가 없기 때문에 더 빠르게 전송할 수 있습니다.
- 지정된 경로의 파일 및 BLOB을 나열/제거합니다.
- 경로에 --include 및 --exclude 플래그 외에도 와일드카드 패턴을 지원합니다.
- 향상된 복원력: 모든 AzCopy 인스턴스는 작업 순서 및 관련 로그 파일을 만듭니다. 이전 작업을 살펴보고 다시 시작하고, 실패한 작업을 재개할 수 있습니다. AzCopy는 전송 실패 후 자동으로 전송을 다시 시도합니다.
- 일반적인 성능이 향상되었습니다.

## <a name="download-and-install-azcopy"></a>AzCopy 다운로드 및 설치

### <a name="latest-preview-version-v10"></a>최신 미리 보기 버전(v10)

AzCopy의 최신 미리 보기 버전을 다운로드합니다.
- [Windows](https://aka.ms/downloadazcopy-v10-windows)
- [Linux](https://aka.ms/downloadazcopy-v10-linux)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac)

### <a name="latest-production-version-v81"></a>최신 프로덕션 버전(v8.1)

[Windows용 AzCopy의 최신 프로덕션 버전](https://aka.ms/downloadazcopy)을 다운로드합니다.

### <a name="azcopy-supporting-table-storage-service-v73"></a>테이블 저장소 서비스(v7.3)를 지원하는 AzCopy

[Microsoft Azure 테이블 저장소 서비스와 데이터 복사를 지원하는 AzCopy v7.3](https://aka.ms/downloadazcopynet)을 다운로드합니다.

## <a name="post-installation-steps"></a>설치 후 단계

AzCopy v10은 설치가 필요하지 않습니다. 선호하는 명령줄 응용 프로그램을 열고 `azcopy.exe` 실행 파일이 있는 폴더로 이동합니다. 원하는 경우 시스템 경로에 AzCopy 폴더 위치를 추가할 수 있습니다.

## <a name="authentication-options"></a>인증 옵션

AzCopy v10을 사용하면 Azure Storage로 인증할 때 다음 옵션을 사용할 수 있습니다.
- Azure Active Directory. Azure Active Directory를 사용하여 ```.\azcopy login```으로 로그인합니다.  사용자가 Azure Active Directory 인증을 사용하여 Blob 저장소에 데이터를 쓰려면 ["Storage Blob 데이터 기여자" 역할이 할당](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)되어야 합니다.
- Blob 경로에 추가해야 하는 SAS 토큰. Azure Portal, [Storage 탐색기](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestorageblobsastoken?view=azurermps-6.9.0) 또는 원하는 다른 도구를 사용하여 SAS 토큰을 생성할 수 있습니다. 자세한 내용은 [예제](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)를 참조하세요.

## <a name="getting-started"></a>시작

AzCopy v10은 간단한 자체 문서화 구문을 갖고 있습니다. 일반 구문은 다음과 같습니다.

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>
# Example:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/containersastoken" --recursive=true
```

다음과 같은 방법으로 사용 가능한 명령 목록을 가져올 수 있습니다.

```azcopy
.\azcopy -help
# Using the alias instead
.\azcopy -h
```

특정 명령에 대한 도움말 페이지 및 예제를 보려면 아래 명령을 실행합니다.

```azcopy
.\azcopy <cmd> -help
# Example:
.\azcopy cp -h
```

## <a name="create-a-file-system-azure-data-lake-storage-gen2-only"></a>파일 시스템(Azure Data Lake Storage Gen2 전용) 만들기

Blob 스토리지 계정에서 계층 구조 네임스페이스를 사용하도록 설정한 경우 다운로드 파일을 업로드할 수 있도록 다음 명령을 사용하여 새 파일 시스템을 만들 수 있습니다.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name" --recursive=true
```

이 문자열의 ``account`` 부분은 스토리지 계정의 이름입니다. 이 문자열의 ``top-level-resource-name`` 부분은 만들려는 파일 시스템의 이름입니다.

## <a name="copy-data-to-azure-storage"></a>Azure Storage에 데이터 복사

복사 명령을 사용하여 원본에서 대상으로 데이터를 전송합니다. 원본/대상으로 사용 가능한 항목은 다음과 같습니다.
- 로컬 파일 시스템
- Azure Blob/가상 디렉터리/컨테이너 URI
- Azure 파일/디렉터리/파일 공유 URI
- Azure Data Lake Storage Gen2 파일 시스템/디렉터리/파일 URI

> [!NOTE]
> 현재 AzCopy v10은 두 저장소 계정 간의 블록 Blob 복사만 지원합니다.

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using alias instead
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

다음 명령은 C:\local\path 폴더의 모든 파일을 "mycontainer1" 컨테이너에 재귀적으로 업로드합니다.

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Blob 스토리지 계정에서 계층 구조 네임스페이스를 사용하도록 설정한 경우 다음 명령을 사용하여 파일 시스템에 파일을 업로드할 수 있습니다.

```azcopy
.\azcopy cp "C:\local\path" "https://myaccount.dfs.core.windows.net/myfolder<sastoken>" --recursive=true
```

다음 명령은 C:\local\path 폴더의 모든 파일을 "mycontainer1" 컨테이너에 업로드합니다(하위 디렉터리에 반복하지 않고).

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

Blob 스토리지 계정에서 계층 구조 네임스페이스를 사용하도록 설정한 경우 다음 명령을 사용할 수 있습니다.

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/myfolder<sastoken>"
```

예제를 더 가져오려면 다음 명령을 사용합니다.

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>두 저장소 계정 간에 데이터 복사

두 저장소 계정 간에 데이터를 복사할 때에는 [Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API를 사용하고 클라이언트 머신의 네트워크 대역폭을 사용하지 않습니다. 두 Azure Storage 서버 간에 데이터가 직접 복사되고, AzCopy는 단순히 복사 작업을 오케스트레이션합니다. 

두 저장소 계정 간에 데이터를 복사하려면 다음 명령을 사용합니다.
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

계층 구조 네임스페이스를 사용하도록 설정한 Blob 스토리지 계정을 사용하려면 이러한 예제에서 ``blob.core.windows.net`` 문자열을 ``dfs.core.windows.net``으로 바꿉니다.

> [!NOTE]
> 이 명령은 모든 Blob 컨테이너를 열거하고 대상 계정에 복사합니다. 현재 AzCopy v10은 두 저장소 계정 간의 블록 Blob 복사만 지원합니다. 그 외의 모든 저장소 계정 개체(추가 Blob, 페이지 Blob, 파일, 테이블 및 큐)를 건너뜁니다.

## <a name="copy-a-vhd-image-to-a-storage-account"></a>VHD 이미지를 저장소 계정에 복사

AzCopy v10은 기본적으로 블록 Blob에 데이터를 업로드합니다. 그러나 원본 파일의 확장명이 vhd이면 AzCopy v10은 기본적으로 페이지 Blob에 업로드합니다. 이 동작은 구성할 수 없습니다.

## <a name="sync-incremental-copy-and-delete"></a>동기화: 증분 복사 및 삭제

> [!NOTE]
> 동기화 명령은 원본의 콘텐츠를 대상과 동기화하며, 여기에는 원본에 없는 대상 파일을 삭제하는 작업이 포함됩니다. 동기화할 대상을 사용해야 합니다.

로컬 파일 시스템을 저장소 계정과 동기화하려면 다음 명령을 사용합니다.

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

같은 방법으로 Blob 컨테이너를 로컬 파일 시스템과 동기화할 수 있습니다.

```azcopy
# If you're using Azure Active Directory authentication the sastoken is not required
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

이 명령을 사용하면 마지막으로 수정된 타임스탬프에 따라 원본을 대상과 증분 방식으로 동기화할 수 있습니다. 원본에서 파일이 추가 또는 삭제되면 AzCopy v10이 대상에서 똑같은 작업을 수행합니다.

[!NOTE] 계층 구조 네임스페이스를 사용하도록 설정한 Blob 스토리지 계정을 사용하려면 이러한 예제에서 ``blob.core.windows.net`` 문자열을 ``dfs.core.windows.net``으로 바꿉니다.

## <a name="advanced-configuration"></a>고급 구성

### <a name="configure-proxy-settings"></a>프록시 설정 구성

AzCopy v10에 대한 프록시 설정을 구성하려면 다음 명령을 사용하여 환경 변수 https_proxy를 설정합니다.

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>처리량 최적화

AZCOPY_CONCURRENCY_VALUE 환경 변수를 설정하여 동시 요청 수를 구성하고 처리량 성능 및 리소스 사용량을 제어할 수 있습니다. 이 값은 기본적으로 300으로 설정됩니다. 값을 줄이면 AzCopy v10에서 사용하는 대역폭 및 CPU가 제한됩니다.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
```

## <a name="troubleshooting"></a>문제 해결

AzCopy v10은 모든 작업에 대한 로그 파일 및 계획 파일을 만듭니다. 로그를 사용하여 잠재적 문제를 조사하고 해결할 수 있습니다. 로그에는 실패 상태(UPLOADFAILED, COPYFAILED 및 DOWNLOADFAILED), 전체 경로, 실패 이유가 포함됩니다. 작업 로그 및 계획 파일은 %USERPROFILE\\.azcopy 폴더에 있습니다.

### <a name="review-the-logs-for-errors"></a>오류에 대한 로그 검토

다음 명령은 04dc9ca9-158f-7945-5933-564021086c79 로그에서 상태가 UPLOADFAILED인 모든 오류를 가져옵니다.

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```

### <a name="view-and-resume-jobs"></a>작업 보기 및 다시 시작

각 전송 작업은 AzCopy 작업을 만듭니다. 다음 명령을 사용하여 작업 기록을 볼 수 있습니다.

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

SAS 토큰(보안상의 이유로 지속되지 않음)과 함께 해당 식별자를 사용하여 실패/취소된 작업을 다시 시작할 수 있습니다.

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

## <a name="next-steps"></a>다음 단계

피드백은 언제든지 환영합니다. 질문, 문제 또는 일반 피드백은 https://github.com/Azure/azure-storage-azcopy에서 제출하시면 됩니다. 감사합니다.