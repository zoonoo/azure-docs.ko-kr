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
ms.openlocfilehash: bfa3e5a943ee59b1ed335f45e113a60f62572675
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735022"
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

AzCopy를 사용 하 여 의미 있는 모든 작업을 수행 하려면, 저장소 서비스에 권한 부여 자격 증명 제공할 방법을 결정 해야 합니다.

## <a name="choose-how-youll-provide-authorization-credentials"></a>권한 부여 자격 증명 제공할 방법 선택

Azure AD (Active Directory)를 사용 하거나 공유 액세스 서명 (SAS) 토큰을 사용 하 여 권한 부여 자격 증명을 제공할 수 있습니다.

이 테이블을 가이드로 사용 합니다.

| 저장소 유형 | 권한 부여의 현재 지원 되는 메서드 |
|--|--|
|**Blob Storage** | Azure AD & SAS |
|**Blob 저장소 (계층적 네임 스페이스)** | Azure AD만 |
|**File Storage** | SAS만 |

### <a name="option-1-use-azure-ad"></a>옵션 1: Azure AD를 사용 합니다.

해야 하는 권한 부여 수준을 기반으로 파일 업로드 또는 다운로드만 것인지 합니다.

#### <a name="authorization-to-upload-files"></a>파일을 업로드 하려면 권한 부여

이러한 역할 중 하나의 id에 할당 되었는지 확인 합니다.

- [Storage Blob 데이터 기여자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Storage Blob 데이터 소유자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

이러한 범위에 있는 id에 이러한 역할을 할당할 수 있습니다.

- 컨테이너 (파일 시스템)
- Storage 계정
- 리소스 그룹
- 구독

참조를 확인 하 고 역할을 할당 하는 방법을 알아보려면 [Azure blob 및 큐 데이터에 RBAC 사용 하 여 Azure portal에서 액세스 권한을 부여](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)합니다.

사용자 id가 대상 컨테이너 또는 디렉터리의 액세스 제어 목록 (ACL)에 추가 하는 경우 사용자의 id에 할당 된 이러한 역할 중 하나를 사용할 필요가 없습니다. ACL을 id에 대 한 대상 디렉터리에 쓰기 및 컨테이너와 각 부모 디렉터리에 대 한 실행 해야 합니다.

자세한 내용은 참조 하세요 [Azure Data Lake 저장소 Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)입니다.

#### <a name="authorization-to-download-files"></a>파일을 다운로드 하려면 권한 부여

이러한 역할 중 하나의 id에 할당 되었는지 확인 합니다.

- [Storage Blob 데이터 읽기 권한자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)
- [Storage Blob 데이터 기여자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Storage Blob 데이터 소유자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

이러한 범위에 있는 id에 이러한 역할을 할당할 수 있습니다.

- 컨테이너 (파일 시스템)
- Storage 계정
- 리소스 그룹
- 구독

참조를 확인 하 고 역할을 할당 하는 방법을 알아보려면 [Azure blob 및 큐 데이터에 RBAC 사용 하 여 Azure portal에서 액세스 권한을 부여](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)합니다.

사용자 id가 대상 컨테이너 또는 디렉터리의 액세스 제어 목록 (ACL)에 추가 하는 경우 사용자의 id에 할당 된 이러한 역할 중 하나를 사용할 필요가 없습니다. ACL을 본인 대상 디렉터리에 대 한 읽기 권한이 및 컨테이너와 각 부모 디렉터리에 대 한 실행 해야 합니다.

자세한 내용은 참조 하세요 [Azure Data Lake 저장소 Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)입니다.

#### <a name="authenticate-your-identity"></a>본인 인증

본인에 필요한 권한 수준을 부여 된를 확인 한 후 명령 프롬프트를 열고, 다음 명령을 입력 하 고 ENTER 키를 누릅니다.

```azcopy
azcopy login
```

이 명령은 웹 사이트의 인증 코드와 URL을 반환합니다. 웹 사이트를 열고, 코드를 입력하고, **다음** 단추를 선택합니다.

![컨테이너 만들기](media/storage-use-azcopy-v10/azcopy-login.png)

로그인 창이 나타납니다. 이 창에서 Azure 계정 자격 증명을 사용하여 Azure 계정에 로그인합니다. 로그인에 성공하면 브라우저 창을 닫고 AzCopy를 사용할 수 있습니다.

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

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>구성, 최적화 및 AzCopy 문제 해결

참조 [구성 최적화 및 AzCopy 문제 해결](storage-use-azcopy-configure.md)

## <a name="use-azcopy-in-storage-explorer"></a>AzCopy를 사용 하 여 Storage 탐색기에서

AzCopy의 성능 이점을 활용 하 여 파일을 사용 하 여 상호 작용 하도록 명령줄 보다는 Storage 탐색기를 사용 하려는 경우, Storage 탐색기에서 AzCopy를 사용 합니다.

Storage 탐색기에서 선택 **미리 보기**->**향상 된 Blob 업로드 및 다운로드에 사용 하 여 AzCopy**합니다.

![Azure Storage 탐색기에서 전송 엔진으로 AzCopy를 사용 하도록 설정](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> 저장소 계정에서 계층적 네임 스페이스를 사용 하도록 설정 하는 경우이 설정을 사용 하도록 설정 필요가 없습니다. 계층적 네임 스페이스를 포함 하는 저장소 계정에서 Storage 탐색기 자동으로 사용 하 여 AzCopy 때문입니다.  

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>이전 버전의 AzCopy 사용 합니다.

이전 버전의 AzCopy (AzCopy v8.1)를 사용 하는 경우 다음 링크 중 하나를 참조 하세요.

- [(V8) Windows에서 AzCopy](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)
- [(V8) Linux에서 AzCopy](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>다음 단계

질문, 문제 또는 일반적인 피드백 경우 제출해 [GitHub에서](https://github.com/Azure/azure-storage-azcopy) 페이지입니다.
