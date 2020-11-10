---
title: AzCopy v10를 사용 하 여 데이터를 Azure Storage 복사 또는 이동 Microsoft Docs
description: AzCopy은 저장소 계정 간에 데이터를 복사 하거나 저장소 계정 간에 데이터를 복사 하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에서는 AzCopy를 다운로드하고, 스토리지 계정에 연결한 다음, 파일을 전송하는 방법을 설명합니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 11/09/2020
ms.author: normesta
ms.subservice: common
ms.custom: contperfq2
ms.openlocfilehash: ad9b40b448b48500cd6882ac614611f91370ec9e
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410274"
---
# <a name="get-started-with-azcopy"></a>AzCopy 시작

AzCopy는 스토리지 계정에서 또는 스토리지 계정으로 Blob 또는 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에서는 AzCopy를 다운로드하고, 스토리지 계정에 연결한 다음, 파일을 전송하는 방법을 설명합니다.

> [!NOTE]
> AzCopy **V10** 는 현재 지원 되는 AzCopy 버전입니다.
>
> 이전 버전의 AzCopy를 사용 해야 하는 경우이 문서의 [이전 버전의 AzCopy 사용](#previous-version) 섹션을 참조 하세요.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>AzCopy 다운로드

먼저 AzCopy V10 실행 파일을 컴퓨터의 모든 디렉터리에 다운로드 합니다. AzCopy V10는 실행 파일 일 뿐 이므로 설치할 항목이 없습니다.

- [Windows 64](https://aka.ms/downloadazcopy-v10-windows) 비트 (zip)
- [Windows 32](https://aka.ms/downloadazcopy-v10-windows-32bit) 비트 (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [Macos](https://aka.ms/downloadazcopy-v10-mac) (zip)

이러한 파일은 zip 파일 (Windows 및 Mac) 또는 tar 파일 (Linux)로 압축 됩니다. Linux에서 tar 파일을 다운로드 하 고 압축을 풀려면 Linux 배포에 대 한 설명서를 참조 하세요.

> [!NOTE]
> [Azure Table storage](../tables/table-storage-overview.md) 서비스로 데이터를 복사 하려면 [AzCopy 버전 7.3](https://aka.ms/downloadazcopynet)을 설치 합니다.


## <a name="run-azcopy"></a>AzCopy 실행

편의상, AzCopy 실행 파일의 디렉터리 위치를 시스템 경로에 추가하여 사용하기 쉽도록 하는 것이 좋습니다. 이렇게 하면 `azcopy` 시스템의 모든 디렉터리에서 입력할 수 있습니다.

AzCopy 디렉터리를 경로에 추가 하지 않도록 선택 하는 경우 AzCopy 실행 파일의 위치로 디렉터리를 변경 하 고 `azcopy` `.\azcopy` Windows PowerShell 명령 프롬프트에서 또는를 입력 해야 합니다.

명령 목록을 보려면를 입력 한 `azcopy -h` 다음 enter 키를 누릅니다.

특정 명령에 대 한 자세한 내용을 보려면 명령의 이름 (예:)을 포함 하면 `azcopy list -h` 됩니다.

> [!div class="mx-imgBorder"]
> ![인라인 도움말](media/storage-use-azcopy-v10/azcopy-inline-help.png)


각 명령 및 명령 매개 변수에 대 한 자세한 참조 설명서를 찾으려면 [azcopy](storage-ref-azcopy.md) 를 참조 하세요.

> [!NOTE] 
> Azure Storage 계정의 소유자는 데이터에 액세스할 수 있는 권한을 자동으로 할당 하지 않습니다. AzCopy를 사용 하 여 의미 있는 작업을 수행 하려면 먼저 저장소 서비스에 권한 부여 자격 증명을 제공 하는 방법을 결정 해야 합니다. 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>AzCopy 권한 부여

AD (Azure Active Directory)를 사용 하거나 SAS (공유 액세스 서명) 토큰을 사용 하 여 권한 부여 자격 증명을 제공할 수 있습니다.

이 표를 가이드로 사용 합니다.

| 스토리지 유형 | 현재 지원 되는 권한 부여 방법 |
|--|--|
|**Blob Storage** | Azure AD 및 SAS |
|**Blob 저장소 (계층적 네임 스페이스)** | Azure AD 및 SAS |
|**File Storage** | SAS 전용 |

#### <a name="option-1-use-azure-active-directory"></a>옵션 1: Azure Active Directory 사용

이 옵션은 blob 저장소에만 사용할 수 있습니다. Azure Active Directory를 사용 하 여 각 명령에 SAS 토큰을 추가 하지 않고 자격 증명을 한 번 제공할 수 있습니다.  

> [!NOTE]
> 현재 릴리스에서 저장소 계정 간에 blob을 복사 하려는 경우 각 원본 URL에 SAS 토큰을 추가 해야 합니다. SAS 토큰은 대상 URL 에서만 생략할 수 있습니다. 예제는 [저장소 계정 간에 Blob 복사](storage-use-azcopy-blobs.md)를 참조 하세요.

Azure AD를 사용 하 여 액세스 권한을 부여 하려면 [AzCopy 및 Azure Active Directory를 사용 하 여 blob에 대 한 액세스 권한 부여 (AZURE ad)](storage-use-azcopy-authorize-azure-active-directory.md)를 참조 하세요.

#### <a name="option-2-use-a-sas-token"></a>옵션 2: SAS 토큰 사용

AzCopy 명령에서 사용 하는 각 원본 또는 대상 URL에 SAS 토큰을 추가할 수 있습니다.

이 예제 명령은 로컬 디렉터리에서 blob 컨테이너로 데이터를 재귀적으로 복사 합니다. 가상의 SAS 토큰이 컨테이너 URL의 끝에 추가 됩니다.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

SAS 토큰 및 SAS 토큰을 구하는 방법에 대 한 자세한 내용은 [sas (공유 액세스 서명) 사용](./storage-sas-overview.md)을 참조 하세요.

## <a name="transfer-data"></a>데이터 전송

Id에 대 한 권한을 부여 하거나 SAS 토큰을 얻은 후에는 데이터 전송을 시작할 수 있습니다.

예제 명령을 찾으려면 다음 문서 중 하나를 참조 하세요.

- [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](storage-use-azcopy-blobs.md)

- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)

- [AzCopy 및 Amazon S3 버킷을 사용하여 데이터 전송](storage-use-azcopy-s3.md)

- [AzCopy 및 Azure Stack 저장소를 사용 하 여 데이터 전송](/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-in-a-script"></a>스크립트에서 사용

#### <a name="obtain-a-static-download-link"></a>정적 다운로드 링크 가져오기

시간이 지남에 따라 AzCopy [다운로드 링크](#download-and-install-azcopy) 는 새 버전의 AzCopy를 가리킵니다. 스크립트에서 AzCopy를 다운로드 하는 경우 새 버전의 AzCopy가 스크립트가 종속 된 기능을 수정 하면 스크립트가 작동을 중지할 수 있습니다.

이러한 문제를 방지 하려면 AzCopy의 현재 버전에 대 한 정적 (변경 되지 않은) 링크를 가져옵니다. 이렇게 하면 스크립트는 실행 될 때마다 동일한 버전의 AzCopy를 다운로드 합니다.

링크를 가져오려면 다음 명령을 실행 합니다.

| 운영 체제  | 명령 |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Linux의 경우 `--strip-components=1` 명령에서 `tar` 버전 이름이 포함 된 최상위 폴더를 제거 하 고 대신 이진 파일을 현재 폴더로 직접 추출 합니다. 이렇게 하면 URL만 업데이트 하 여 새 버전의로 스크립트를 업데이트할 수 있습니다 `azcopy` `wget` .

URL은이 명령의 출력에 표시 됩니다. 그런 다음 스크립트에서 해당 URL을 사용 하 여 AzCopy를 다운로드할 수 있습니다.

| 운영 체제  | 명령 |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>SAS 토큰의 이스케이프 특수 문자

확장명이 있는 배치 파일에서 `.cmd` `%` SAS 토큰에 표시 되는 문자를 이스케이프 해야 합니다. `%`SAS 토큰 문자열의 기존 문자 옆에 추가 문자를 추가 하 여이 작업을 수행할 수 있습니다 `%` .

#### <a name="run-scripts-by-using-jenkins"></a>Jenkins를 사용 하 여 스크립트 실행

[Jenkins](https://jenkins.io/) 를 사용 하 여 스크립트를 실행 하려는 경우 스크립트의 시작 부분에 다음 명령을 입력 해야 합니다.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Azure Storage 탐색기에서 사용

[Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/) AzCopy를 사용 하 여 모든 데이터 전송 작업을 수행 합니다. AzCopy의 성능 이점을 활용 하려는 경우에는 [Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/) 를 사용할 수 있지만 명령줄이 아닌 그래픽 사용자 인터페이스를 사용 하 여 파일을 조작 하는 것이 좋습니다.

Storage 탐색기는 계정 키를 사용 하 여 작업을 수행 하므로 Storage 탐색기에 로그인 한 후에는 추가 권한 부여 자격 증명을 제공할 필요가 없습니다.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>구성, 최적화 및 수정

[AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md) 을 참조 하세요.

## <a name="use-a-previous-version"></a>이전 버전 사용

이전 버전의 AzCopy를 사용 해야 하는 경우 다음 링크 중 하나를 참조 하세요.

- [Windows에서 AzCopy(v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [Linux의 AzCopy (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>다음 단계

질문, 문제 또는 일반 피드백이 있는 경우 [GitHub 페이지에서](https://github.com/Azure/azure-storage-azcopy) 제출 합니다.