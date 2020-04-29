---
title: 로그를 사용 하 여 데이터 업로드 문제 해결
titleSuffix: Azure Data Box Disk
description: 로그를 사용 하 여 Azure Data Box Disk 데이터를 업로드할 때 표시 되는 문제를 해결 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7c14988706ef193ef5da868c55f6c4f55e7d98f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79260138"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Azure Data Box Disk에서 데이터 업로드 문제를 해결 하기 위한 로그 이해

이 문서는 Microsoft Azure Data Box Disk에 적용 되며 데이터를 Azure에 업로드할 때 표시 되는 문제에 대해 설명 합니다.

## <a name="about-upload-logs"></a>로그 업로드 정보

데이터 센터 `_error.xml` 에서 Azure에 데이터를 업로드 하면 각 저장소 계정 `_verbose.xml` 에 대해 파일이 생성 됩니다. 이러한 로그는 데이터를 업로드 하는 데 사용 된 것과 동일한 저장소 계정에 업로드 됩니다. 

두 로그는 모두 동일한 형식이 며 디스크에서 Azure Storage 계정으로 데이터를 복사 하는 동안 발생 한 이벤트에 대 한 XML 설명을 포함 합니다.

자세한 정보 표시 로그는 모든 blob 또는 파일에 대 한 복사 작업의 상태에 대 한 전체 정보를 포함 하는 반면 오류 로그에는 업로드 중에 오류가 발생 한 blob 또는 파일에 대 한 정보만 포함 됩니다.

오류 로그는 자세한 로그와 동일한 구조를 갖지만 성공적인 작업을 필터링 합니다.

## <a name="download-logs"></a>로그 다운로드

업로드 로그를 찾으려면 다음 단계를 수행 합니다.

1. Azure에 데이터를 업로드할 때 오류가 발생 하는 경우 포털은 진단 로그가 있는 폴더에 대 한 경로를 표시 합니다.

    ![포털의 로그에 연결](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. **Waies**이동 합니다.

    ![오류 및 자세한 정보 표시 로그](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

각 경우에 오류 로그와 자세한 로그를 볼 수 있습니다. 각 로그를 선택 하 고 로컬 복사본을 다운로드 합니다.

## <a name="sample-upload-logs"></a>샘플 업로드 로그

예제 `_verbose.xml` 는 다음과 같습니다. 이 경우에는 오류 없이 주문이 성공적으로 완료 되었습니다.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

동일한 순서로의 샘플 `_error.xml` 은 다음과 같습니다.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

아래에는 `_error.xml` 주문이 완료 되었으나 오류가 발생 한 샘플이 나와 있습니다. 

이 경우 오류 파일에는 `Summary` 섹션과 모든 파일 수준 오류를 포함 하는 다른 섹션이 있습니다. 

에 `Summary` 는 `ValidationErrors` 및가 포함 `CopyErrors`됩니다. 이 경우 8 개의 파일이 나 폴더가 Azure에 업로드 되 고 유효성 검사 오류가 발생 하지 않습니다. 데이터가 Azure Storage 계정에 복사 되 면 5 개의 파일이 나 폴더가 성공적으로 업로드 됩니다. 나머지 3 개의 파일 또는 폴더는 Azure 컨테이너 명명 규칙에 따라 이름이 바뀌고 Azure에 성공적으로 업로드 됩니다.

파일 수준 상태는 blob을 `BlobStatus` 업로드 하기 위해 수행 된 작업을 설명 하는에 있습니다. 이 경우 데이터가 복사 된 폴더가 컨테이너의 Azure 명명 규칙을 준수 하지 않기 때문에 세 개의 컨테이너 이름이 바뀝니다. 이러한 컨테이너에서 업로드 된 blob의 경우 새 컨테이너 이름, Azure의 blob 경로, 잘못 된 원본 파일 경로 및 blob 크기가 포함 됩니다.
    
```xml
 <?xml version="1.0" encoding="utf-8"?>
    <DriveLog Version="2018-10-01">
      <DriveId>18041C582D7E</DriveId>
      <Summary>
     <!--Summary for validation and data copy to Azure -->
        <ValidationErrors>
          <None Count="8" />
        </ValidationErrors>
        <CopyErrors>
          <Completed Count="5" Description="No errors encountered" />
          <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
        </CopyErrors>
      </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
        <SizeInBytes>71528448</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Status>CompletedWithErrors</Status>
    </DriveLog>
```

## <a name="data-upload-errors"></a>데이터 업로드 오류

Azure에 데이터를 업로드할 때 생성 되는 오류는 다음 표에 요약 되어 있습니다.

| 오류 코드 | Description                   |
|-------------|------------------------------|
|`None` |  성공적으로 완료 되었습니다.           |
|`Renamed` | Blob의 이름을 바꿨습니다.   |
|`CompletedWithErrors` | 업로드가 완료 되었으나 오류가 발생 했습니다. 오류가 발생 한 파일에 대 한 세부 정보는 로그 파일에 포함 됩니다.  |
|`Corrupted`|데이터 수집 중에 계산 된 CRC가 업로드 중에 계산 된 CRC와 일치 하지 않습니다.  |  
|`StorageRequestFailed` | Azure storage 요청이 실패 했습니다.   |     
|`LeasePresent` | 이 항목은 임대 되며 다른 사용자가 사용 하 고 있습니다. |
|`StorageRequestForbidden` |인증 문제로 인해 업로드할 수 없습니다. |
|`ManagedDiskCreationTerminalFailure` | 관리 디스크로 업로드할 수 없습니다. 파일은 준비 저장소 계정에서 페이지 blob으로 사용할 수 있습니다. 페이지 blob을 관리 디스크로 수동으로 변환할 수 있습니다.  |
|`DiskConversionNotStartedTierInfoMissing` | VHD 파일이 미리 생성 된 계층 폴더 외부에 복사 되었으므로 관리 디스크는 생성 되지 않았습니다. 파일은 주문 생성 중에 지정 된 대로 준비 저장소 계정에 페이지 blob으로 업로드 됩니다. 수동으로 관리 되는 디스크로 변환할 수 있습니다.|
|`InvalidWorkitem` | Azure 명명 및 제한 규칙에 맞지 않으므로 데이터를 업로드할 수 없습니다.|
|`InvalidPageBlobUploadAsBlockBlob` | 접두사가 `databoxdisk-invalid-pb-`있는 컨테이너에 블록 blob으로 업로드 됩니다.|
|`InvalidAzureFileUploadAsBlockBlob` | 접두사 `databoxdisk-invalid-af`-를 사용 하 여 컨테이너에 블록 blob으로 업로드 됩니다.|
|`InvalidManagedDiskUploadAsBlockBlob` | 접두사 `databoxdisk-invalid-md`-를 사용 하 여 컨테이너에 블록 blob으로 업로드 됩니다.|
|`InvalidManagedDiskUploadAsPageBlob` |접두사가 `databoxdisk-invalid-md-`있는 컨테이너에서 페이지 blob으로 업로드 됩니다. |
|`MovedToOverflowShare` |원본 공유 크기가 최대 Azure 크기 제한을 초과 하 여 새 공유에 파일을 업로드 했습니다. 새 파일 공유 이름의 원래 이름에는 접미사가 붙습니다 `-2`.   |
|`MovedToDefaultAzureShare` |폴더에 포함 되지 않은 파일을 기본 공유로 업로드 했습니다. 공유 이름은로 `databox-`시작 합니다. |
|`ContainerRenamed` |이러한 파일의 컨테이너는 Azure 명명 규칙을 준수 하지 않으며 이름이 바뀝니다. 새 이름은로 `databox-` 시작 하 고 원래 이름의 SHA1 해시를 접미사로 사용 합니다. |
|`ShareRenamed` |이러한 파일에 대 한 공유는 Azure 명명 규칙을 준수 하지 않으며 이름이 바뀝니다. 새 이름은로 `databox-` 시작 하 고 원래 이름의 SHA1 해시를 접미사로 사용 합니다. |
|`BlobRenamed` |이러한 파일은 Azure 명명 규칙을 준수 하지 않으며 이름이 바뀌었습니다. 새 이름 `BlobPath` 에 대 한 필드를 확인 합니다. |
|`FileRenamed` |이러한 파일은 Azure 명명 규칙을 준수 하지 않으며 이름이 바뀌었습니다. 새 이름 `FileStoragePath` 에 대 한 필드를 확인 합니다. |
|`DiskRenamed` |이러한 파일은 Azure 명명 규칙을 준수 하지 않으며 이름이 바뀌었습니다. 새 이름 `BlobPath` 에 대 한 필드를 확인 합니다. |


## <a name="next-steps"></a>다음 단계

- [Data Box Disk 문제에 대 한 지원 티켓을 엽니다](data-box-disk-contact-microsoft-support.md).
