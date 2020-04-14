---
title: 아즈카피 카피 | 마이크로 소프트 문서
description: 이 문서에서는 azcopy 복사 명령에 대한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0325a71fb069f3d96f05d106afac1639fc38fe42
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253342"
---
# <a name="azcopy-copy"></a>azcopy copy

원본 데이터를 대상 위치로 복사합니다.

## <a name="synopsis"></a>개요

원본 데이터를 대상 위치로 복사합니다. 지원되는 방향은 다음과 같습니다.

  - 로컬 < > Azure Blob(SAS 또는 OAuth 인증)
  - 로컬 < > Azure 파일(공유/디렉터리 SAS 인증)
  - 로컬 < > ADLS Gen 2(SAS, OAuth 또는 공유 키 인증)
  - Azure Blob(SAS 또는 공용) -> Azure Blob(SAS 또는 OAuth 인증)
  - Azure Blob(SAS 또는 공용) -> Azure 파일(SAS)
  - Azure 파일(SAS) -> Azure 파일(SAS)
  - Azure 파일(SAS) -> Azure Blob(SAS 또는 OAuth 인증)
  - AWS S3(액세스 키) -> Azure 블록 Blob(SAS 또는 OAuth 인증)

자세한 내용은 예제를 참조하십시오.

## <a name="related-conceptual-articles"></a>관련 개념 기사

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 저장소로 데이터 전송](storage-use-azcopy-blobs.md)
- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)

## <a name="advanced"></a>고급

AzCopy는 파일 확장명또는 콘텐츠(확장확장이 지정되지 않은 경우)에 따라 로컬 디스크에서 업로드할 때 파일의 콘텐츠 유형을 자동으로 감지합니다.

기본 제공 조회 테이블은 작지만 유닉스에서는 로컬 시스템의 mime.type 파일(들)에 의해 보강되어 이러한 이름 중 하나 이상에서 사용할 수 있습니다.

- /etc/mime.type
- /etc/아파치2/마임.type
- /etc/아파치/마임.type

Windows에서 MIME 형식은 레지스트리에서 추출됩니다. 이 기능은 플래그의 도움으로 해제할 수 있습니다. 플래그 섹션을 참조하십시오.

명령줄을 사용하여 환경 변수를 설정하면 명령줄 기록에서 해당 변수를 읽을 수 있습니다. 명령줄 기록의 자격 증명이 포함된 변수를 지우는 것이 좋습니다. 기록에 변수가 나타나지 않도록 하려면 스크립트를 사용하여 사용자에게 자격 증명을 묻고 환경 변수를 설정할 수 있습니다.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>예

OAuth 인증을 사용하여 단일 파일을 업로드합니다. AzCopy에 아직 로그인하지 않은 경우 다음 명령을 실행하기 전에 azcopy 로그인 명령을 실행하십시오.

- azcopy cp "/경로/to/file.txt" "https://[계정].blob.core.net/[컨테이너]/[경로/경로/blob]"

위와 동일하지만 이번에는 파일 콘텐츠의 MD5 해시를 계산하고 Blob의 Content-MD5 속성으로 저장합니다.

- azcopy cp "/경로/to/file.txt" "https://[계정].blob.core.net/[컨테이너]/[경로/에/blob]" --put-md5

SAS 토큰을 사용하여 단일 파일을 업로드합니다.

- azcopy cp "/경로/에/file.txt" "https://[계정].blob.core.net/[컨테이너]/[경로/에/blob]? [SAS]"

SAS 토큰 및 파이핑을 사용하여 단일 파일을 업로드합니다(블록 Blob만 사용):
  
- 고양이 "/경로/에/file.txt" | azcopy cp "https://[계정].blob.core.net/[컨테이너]/[경로/에/blob]? [SAS]"

SAS 토큰을 사용하여 전체 디렉터리 업로드:
  
- azcopy cp "/경로/에/dir" "https://[계정].blob.core.windows.net/[컨테이너]/[경로/경로/디렉토리]? [SAS]" --재귀=true

또는

- azcopy cp "/경로/에/dir" "https://[계정].blob.core.windows.net/[컨테이너]/[경로/경로/디렉토리]? [SAS]" --재귀=true --put-md5

SAS 토큰 및 와일드카드(*) 문자를 사용하여 파일 집합을 업로드합니다.

- azcopy cp "/경로/foo/bar/*.pdf" "https://[계정].blob.core.net/[컨테이너]/[경로/to/directory]?*foo/* [SAS]"

SAS 토큰 및 와일드카드(*) 문자를 사용하여 파일 및 디렉터리 업로드:

- azcopy cp *"/경로/foo/바*"*"https://[계정].blob.core.net/[컨테이너]/[경로/경로/디렉토리]? [SAS]" --재귀=true

OAuth 인증을 사용하여 단일 파일을 다운로드합니다. AzCopy에 아직 로그인하지 않은 경우 다음 명령을 실행하기 전에 azcopy 로그인 명령을 실행하십시오.

- azcopy cp "https://[계정].blob.core.net/[컨테이너]/[경로/에/blob]" "/경로/에/file.txt"

SAS 토큰을 사용하여 단일 파일을 다운로드합니다.

- azcopy cp "https://[계정].blob.core.net/[컨테이너]/[경로/에/blob]? [SAS]" "/경로/파일.txt"

SAS 토큰을 사용하여 단일 파일을 다운로드한 다음 출력을 파일로 파이핑합니다(블록 Blob만 사용).
  
- azcopy cp "https://[계정].blob.core.net/[컨테이너]/[경로/에/blob]? [SAS]" > "/경로/에/file.txt"

SAS 토큰을 사용하여 전체 디렉토리를 다운로드합니다.
  
- azcopy cp "https://[계정].blob.core.net/[컨테이너]/[경로/경로/에/디렉토리]? [SAS]" "/경로/에/dir" --재귀=true

URL에서 와일드카드 문자(*)를 사용하는 것에 대한 참고 사항:

URL에서 와일드카드 문자를 사용하는 지원되는 방법은 두 가지뿐입니다. 

- URL의 최종 정방향 슬래시(/) 바로 이후에 하나를 사용할 수 있습니다. 이렇게 하면 디렉터리에 있는 모든 파일을 하위 디렉터리로 배치하지 않고 대상에 직접 복사합니다.

- URL이 Blob이 아닌 컨테이너만 참조하는 한 컨테이너 이름에 하나를 사용할 수도 있습니다. 이 방법을 사용하여 컨테이너의 하위 집합에서 파일을 가져올 수 있습니다.

포함된 디렉터리 자체를 복사하지 않고 디렉터리 내용을 다운로드합니다.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[컨테이너]/[경로/에/폴더]/*? [SAS]" "/경로/경로/디르"

전체 저장소 계정을 다운로드합니다.

- azcopy cp "https://[srcaccount].blob.core.windows.net/" "/경로/to/dir" --재귀

컨테이너 이름에 와일드카드 기호(*)를 사용하여 저장소 계정 내에서 컨테이너의 하위 집합을 다운로드합니다.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[컨테이너*이름]" "/path/to/dir" --재귀

SAS 토큰을 사용하여 단일 Blob을 다른 Blob에 복사합니다.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[컨테이너]/[경로/에/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[컨테이너]/[경로/에/blob]? [SAS]"

SAS 토큰과 OAuth 토큰을 사용하여 단일 Blob을 다른 Blob에 복사합니다. 원본 계정 URL 끝에 SAS 토큰을 사용해야 하지만 azcopy 로그인 명령을 사용하여 AzCopy에 로그인하는 경우 대상 계정이 필요하지 않습니다. 

- azcopy cp "https://[srcaccount].blob.core.windows.net/[컨테이너]/[경로/에/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[컨테이너]/[경로/에/blob]"

SAS 토큰을 사용하여 한 Blob 가상 디렉터리를 다른 Blob 가상 디렉터리로 복사합니다.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[컨테이너]/[경로/에/디렉토리]? [SAS]" "https://[destaccount].blob.core.windows.net/[컨테이너]/[경로/에/디렉토리]? [SAS]" --재귀=true

SAS 토큰을 사용하여 저장소 계정에서 다른 Blob 컨테이너, 디렉터리 및 Blob을 복사합니다.

- azcopy CP "https://[srcaccount].blob.core.windows.net? [SAS]" "https://[destaccount].blob.core.windows.net? [SAS]" --재귀=true

액세스 키와 SAS 토큰을 사용하여 Amazon Web Services(AWS) S3에서 단일 개체를 Blob Storage에 복사합니다. 먼저 AWS S3 소스에 대한 환경 변수 AWS_ACCESS_KEY_ID 및 AWS_SECRET_ACCESS_KEY 설정합니다.
  
- azcopy cphttps://s3.amazonaws.com/" [버킷]/[개체]" "https://[destaccount].blob.core.net/[컨테이너]/[경로/에/blob]? [SAS]"

액세스 키와 SAS 토큰을 사용하여 AWS S3에서 전체 디렉터리에서 Blob Storage로 복사합니다. 먼저 AWS S3 소스에 대한 환경 변수 AWS_ACCESS_KEY_ID 및 AWS_SECRET_ACCESS_KEY 설정합니다.

- azcopy cphttps://s3.amazonaws.com/" [버킷]/[폴더]" "https://[destaccount].blob.core.net/[컨테이너]/[경로/경로/디렉토리]? [SAS]" --재귀=true

[폴더] https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html 자리 표시자를 더 잘 이해하려면 참조하십시오.

액세스 키와 SAS 토큰을 사용하여 모든 버킷을 Amazon 웹 서비스(AWS)의 Blob Storage로 복사합니다. 먼저 AWS S3 소스에 대한 환경 변수 AWS_ACCESS_KEY_ID 및 AWS_SECRET_ACCESS_KEY 설정합니다.

- azcopy cphttps://s3.amazonaws.com/" " "https://[destaccount].blob.core.windows.net? [SAS]" --재귀=true

액세스 키와 SAS 토큰을 사용하여 Amazon 웹 서비스(AWS) 리전에서 모든 버킷을 Blob Storage로 복사합니다. 먼저 AWS S3 소스에 대한 환경 변수 AWS_ACCESS_KEY_ID 및 AWS_SECRET_ACCESS_KEY 설정합니다.

- azcopy cphttps://s3-" [지역].amazonaws.com/" "https://[destaccount].blob.core.net? [SAS]" --재귀=true

버킷 이름에 와일드카드 기호(*)를 사용하여 버킷의 하위 집합을 복사합니다. 이전 예제와 마찬가지로 액세스 키와 SAS 토큰이 필요합니다. AWS S3 소스에 대한 환경 변수 AWS_ACCESS_KEY_ID 및 AWS_SECRET_ACCESS_KEY 설정해야 합니다.

- azcopy cphttps://s3.amazonaws.com/" [버킷*이름]/" "https://[destaccount].blob.core.net? [SAS]" --재귀=true

## <a name="options"></a>옵션

**--백업**                               업로드에 대 한 Windows의 SeBackupPrivilege 활성화, 또는 다운로드에 대 한 SeRestorePrivilege, AzCopy 모든 파일을 읽을 수 있도록 허용, 그들의 파일 시스템 권한에 관계 없이, 모든 권한을 복원. AzCopy를 실행하는 계정에 이미 이러한 권한이 있어야 합니다(예: 관리자 권한이 있거나 '백업 운영자' 그룹의 구성원). 이 플래그는 계정에 이미 있는 권한을 활성화하는 것입니다.

**--Blob 형식** 문자열 대상에서 Blob 의 형식을 정의합니다. 이는 Blob을 업로드하고 계정 간에 복사할 때 사용됩니다(기본 '감지'). 유효한 값으로는 '감지', '블록블랍', 'PageBlob', 'AppendBlob'가 있습니다. 계정 간에 복사할 때 '감지' 값을 사용하면 AzCopy에서 소스 Blob 유형을 사용하여 대상 Blob의 유형을 결정합니다. 파일을 업로드할 때 '감지'는 파일 확장명에 따라 파일이 VHD 또는 VHDX 파일인지 여부를 결정합니다. 파일이 VHD 또는 VHDX 파일인 경우 AzCopy는 파일을 페이지 Blob로 처리합니다. (기본값 "감지")

**--블록-Blob 계층** 문자열 블록 Blob을 선택한 [액세스 계층에](../blobs/storage-blob-storage-tiers.md) 직접 업로드합니다. (기본값 '없음')을 입력합니다. 유효한 값에는 '없음', '핫', '쿨', '아카이브'가 포함됩니다. '없음' 또는 계층이 전달되지 않으면 Blob은 저장소 계정의 계층을 상속합니다.

**--블록 크기-mb** 부동 Azure 저장소에 업로드 하 고 Azure 저장소에서 다운로드할 때 이 블록 크기(MiB에 지정)를 사용합니다. 기본값은 파일 크기에 따라 자동으로 계산됩니다. 소수분은 허용됩니다(예: 0.25).

**--캐시 제어** 문자열 캐시 제어 헤더를 설정합니다. 다운로드 시 반환되었습니다.

**--체크 길이**                         전송 후 대상에 있는 파일의 길이를 확인합니다. 원본과 대상 간에 불일치가 있는 경우 전송은 실패한 것으로 표시됩니다. (기본 참)

**--check-md5** 문자열은 다운로드 할 때 MD5 해시의 유효성을 엄격하게 검사하는 방법을 지정합니다. 다운로드 할 때만 사용할 수 있습니다. 사용 가능한 옵션: 없음 검사, 로그만, FailIfDifferent, FailIfDifferentOrMissing. (기본값 "FailIfDifferent")

**--콘텐츠 처리** 문자열 콘텐츠 처리 헤더를 설정합니다. 다운로드 시 반환되었습니다.

**--콘텐츠 인코딩** 문자열 콘텐츠 인코딩 헤더를 설정합니다. 다운로드 시 반환되었습니다.

**--콘텐츠 언어** 문자열 콘텐츠 언어 헤더를 설정합니다. 다운로드 시 반환되었습니다.

**--콘텐츠 유형** 문자열은 파일의 콘텐츠 형식을 지정합니다. 추측-마임 형이 없음을 의미합니다. 다운로드 시 반환되었습니다.

**--압축 해제**                           다운로드 할 때 파일의 콘텐츠 인코딩이 압축되어 있음을 나타내는 경우 파일을 자동으로 압축 해제합니다. 지원되는 콘텐츠 인코딩 값은 'gzip' 및 'deflate'입니다. '.gz'/'gzip' 또는 '.zz'의 파일 확장자는 필요하지 않지만 있는 경우 제거됩니다.

**--제외 특성** 문자열(Windows만 해당) 특성이 특성 목록과 일치하는 파일을 제외합니다. 예를 들어: A; S; R

**--제외 Blob 유형** 문자열 선택적으로 컨테이너 또는 계정에서 Blob을 복사할 때 제외할 Blob 유형(BlockBlob/ PageBlob/ AppendBlob)을 지정합니다. 이 플래그의 사용은 Azure 서비스가 아닌 서비스에서 서비스로 데이터를 복사하는 데 는 적용되지 않습니다. 두 개 이상의 Blob은 ';'로 구분되어야 합니다.

**--제외 경로** 문자열 복사할 때 이러한 경로를 제외합니다. 이 옵션은 와일드카드 문자(*)를 지원하지 않습니다. 상대 경로 접두사를 확인합니다(예: myFolder;myFolder/subDirName/file.pdf). 계정 통과와 함께 사용하는 경우 경로에는 컨테이너 이름이 포함되지 않습니다.

**--제외 패턴** 문자열 복사 할 때 이러한 파일을 제외합니다. 이 옵션은 와일드카드 문자(*)를 지원합니다.

**--팔로우 심볼링크**                      로컬 파일 시스템에서 업로드할 때 기호 링크를 따릅니다.

**--에서** 문자열선택적으로 소스 대상 조합을 지정합니다. 예: 로컬Blob, BlobLocal, 로컬BlobFS.

**-h, --도움말** 복사에 대 한 도움말

**--포함 특성** 문자열(Windows만) 특성이 특성 목록과 일치하는 파일을 포함합니다. 예를 들어: A; S; R

**--포함 경로** 문자열 복사할 때 이러한 경로만 포함합니다. 이 옵션은 와일드카드 문자(*)를 지원하지 않습니다. 상대 경로 접두사를 확인합니다(예: myFolder;myFolder/subDirName/file.pdf).

**--포함 패턴** 문자열 복사 할 때 이러한 파일만 포함합니다. 이 옵션은 와일드카드 문자(*)를 지원합니다. ';'를 사용하여 파일을 분리합니다.

**--로그 수준** 문자열 로그 파일, 사용 가능한 수준: INFO(모든 요청/응답), 경고(느린 응답), ERROR(실패한 요청만), NONE(출력 로그 없음)에 대한 로그 세부 정보를 정의합니다. (기본값 "정보")

**--메타데이터** 문자열 이러한 키-값 쌍을 메타데이터로 사용하여 Azure Storage에 업로드합니다.

**--추측 마임 타입**                   AzCopy가 파일의 확장 또는 내용에 따라 콘텐츠 형식을 검색하지 못하도록 합니다.

--이 플래그가 true로 설정된 경우 대상에서 충돌하는 파일과 Blob을 **덮어씁니다.** 가능한 값으로는 'true', 'false', 'ifSourceNewer', '프롬프트'가 있습니다. (기본값 "true")

**--페이지-Blob 계층** 문자열 이 Blob 계층을 사용하여 페이지 Blob을 Azure 저장소에 업로드합니다. (기본값 "없음")

**--보존 - 마지막 수정 시간**          대상이 파일 시스템인 경우에만 사용할 수 있습니다.

**--보존-smb-권한** 문자열 기본적으로 False. 인식 리소스(Windows 및 Azure 파일) 간에 SMB ACL을 보존합니다. 다운로드의 경우 플래그를 `--backup` 사용하여 새 소유자가 AzCopy를 실행하는 사용자가 아닌 권한을 복원해야 합니다. 이 플래그는 파일 전용 필터를 지정하지 않는 한 파일과 폴더 `include-pattern`모두에 적용됩니다(예: ).

**--보존-smb-정보** 문자열 기본적으로 False. SMB 인식 리소스(Windows 및 Azure 파일) 간에 SMB 속성 정보(마지막 쓰기 시간, 생성 시간, 특성 비트)를 유지합니다. Azure 파일에서 지원하는 특성 비트만 전송됩니다. 다른 사람은 무시됩니다. 이 플래그는 파일 전용 필터(예: 포함 패턴)를 지정하지 않는 한 파일과 폴더 모두에 적용됩니다. 폴더에 대해 전송된 정보는 폴더에 대해 보존되지 않는 마지막 쓰기 시간을 제외하고 파일의 정보와 동일합니다.

**--보존 소유자**                       데이터를 다운로드할 때만, 그리고 이 가 `--preserve-smb-permissions` 사용될 때만 효과가 있다. true(기본값)이면 파일 소유자 및 그룹은 다운로드에서 유지됩니다. 이 플래그가 false로 `--preserve-smb-permissions` 설정된 경우에도 ACL은 유지되지만 소유자 및 그룹은 AzCopy를 실행하는 사용자를 기반으로 합니다.

**--put-md5**                             각 파일의 MD5 해시를 만들고 해시를 대상 Blob 또는 파일의 Content-MD5 속성으로 저장합니다. (기본적으로 해시가 만들어지지 않습니다.) 업로드할 때만 사용할 수 있습니다.

**--재귀**                            로컬 파일 시스템에서 업로드할 때 하위 디렉터리를 재귀적으로 살펴봅니다.

**--s2s 감지 소스 변경**           를 지정한 후 소스가 변경되었는지 확인합니다.

**--s2s 핸들-잘못된 메타데이터** 문자열은 잘못된 메타데이터 키를 처리하는 방법을 지정합니다. 사용 가능한 옵션: 제외IfIn유효, FailIfInid, 이름변경유효. (기본값 "제외된 경우 유효하지 않음")

**--s2s-보존 액세스 계층**             서비스 복사본에 서비스하는 동안 액세스 계층을 보존합니다. [Azure Blob 저장소: 핫, 쿨 및 아카이브 액세스 계층을](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) 참조하여 대상 저장소 계정이 액세스 계층 설정설정을 지원하는지 확인하십시오. 액세스 계층 설정이 지원되지 않는 경우 s2sPreserveAccessTier=false를 사용하여 복사 액세스 계층을 우회하십시오. (기본 참)

**--s2s 보존 속성**              서비스 복사본에 서비스 하는 동안 전체 속성을 유지 합니다. AWS S3 및 Azure File 비단일 파일 소스의 경우 목록 작업이 개체 및 파일의 전체 속성을 반환하지 않습니다. 전체 속성을 보존하려면 AzCopy는 개체 또는 파일당 하나의 추가 요청을 보내야 합니다. (기본 참)

## <a name="options-inherited-from-parent-commands"></a>상위 명령에서 상속된 옵션

**--캡 mbps uint32**      초당 메가비트로 전송 속도를 한도 를 캡슐화합니다. 모멘트별 처리량은 캡과 약간 다를 수 있습니다. 이 옵션이 0으로 설정되어 있거나 생략된 경우 처리량은 제한되지 않습니다.

**--출력 유형** 문자열 명령출력의 형식입니다. 선택 사항은 다음과 같습니다: 텍스트, json. 기본값은 '텍스트'입니다. (기본값 "텍스트")

## <a name="see-also"></a>참고 항목

- [azcopy](storage-ref-azcopy.md)
