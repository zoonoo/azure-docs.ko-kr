---
title: 로그를 사용하여 데이터 업로드 문제 해결
titleSuffix: Azure Data Box Disk
description: 로그를 사용하고 데이터를 Azure Data Box Disk에 업로드할 때 발생하는 문제를 해결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 4b53cf607bdf60c785c7324d9ede526a0983b7e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97605278"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Azure Data Box Disk에서 데이터 업로드 문제를 해결하기 위한 로그 이해

이 문서는 Microsoft Azure Data Box Disk에 적용되며 데이터를 Azure에 업로드할 때 발생하는 문제에 대해 설명합니다.

## <a name="about-upload-logs"></a>로그 업로드 정보

데이터가 데이터 센터에서 Azure에 업로드되면 각 스토리지 계정에 대해 `_error.xml`과 `_verbose.xml` 파일이 생성됩니다. 이러한 로그는 데이터를 업로드하는 데 사용된 동일한 스토리지 계정에 업로드됩니다. 

두 로그는 모두 동일한 형식이며 디스크에서 Azure Storage 계정으로 데이터를 복사하는 동안 발생한 이벤트에 대한 XML 설명을 포함합니다.

자세한 정보 표시 로그는 모든 Blob 또는 파일에 대한 복사 작업의 상태에 대한 전체 정보를 포함하는 반면, 오류 로그에는 업로드 중에 오류가 발생한 Blob 또는 파일에 대한 정보만 포함됩니다.

오류 로그는 자세한 정보 로그와 구조가 동일하지만 성공적인 작업을 필터링합니다.

## <a name="download-logs"></a>로그 다운로드

업로드 로그를 찾으려면 다음 단계를 수행합니다.

1. Azure에 데이터를 업로드할 때 오류가 발생하면 포털에 진단 로그가 있는 폴더의 경로가 표시됩니다.

    ![포털의 로그에 연결](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. **waies** 로 이동합니다.

    ![오류 및 자세한 로그](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

각 경우에 오류 로그와 자세한 로그가 표시됩니다. 각 로그 선택 및 로컬 복사본 다운로드.

## <a name="sample-upload-logs"></a>샘플 업로드 로그

`_verbose.xml`에 대한 샘플은 다음과 같습니다. 이 경우, 주문이 오류 없이 성공적으로 완료되었습니다.

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

동일한 주문에 대해 `_error.xml`의 샘플이 아래에 표시됩니다.

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

아래에는 오류와 함께 주문이 완료된 `_error.xml`의 샘플이 표시됩니다. 

이 경우 오류 파일에는 `Summary` 섹션과 모든 파일 수준 오류가 포함된 다른 섹션이 있습니다. 

`Summary`에는 `ValidationErrors` 및 `CopyErrors`이 포함되어 있습니다. 이 경우 8개의 파일 또는 폴더가 Azure에 업로드되었으며 유효성 검사 오류가 없었습니다. 데이터가 Azure Storage 계정에 복사되면 5개의 파일이나 폴더가 성공적으로 업로드됩니다. 나머지 3개의 파일 또는 폴더는 Azure 컨테이너 명명 규칙에 따라 이름이 바뀌고 Azure에 성공적으로 업로드됩니다.

파일 수준 상태는 blob을 업로드하기 위해 수행된 작업을 설명하는 `BlobStatus`에 있습니다. 이 경우 데이터가 복사된 폴더가 컨테이너의 Azure 명명 규칙을 준수하지 않기 때문에 세 개의 컨테이너 이름이 바뀝니다. 이러한 컨테이너에서 업로드된 blob의 경우 새 컨테이너 이름, Azure의 blob 경로, 잘못된 원본 파일 경로 및 blob 크기가 포함됩니다.
  
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

Azure에 데이터를 업로드할 때 생성되는 오류는 다음 표에 요약되어 있습니다.

| 오류 코드 | Description                   |
|-------------|------------------------------|
|`None` |  성공적으로 완료됨.           |
|`Renamed` | Blob의 이름을 바꿨습니다.   |
|`CompletedWithErrors` | 업로드가 완료되었지만 오류가 발생했습니다. 오류가 발생한 파일에 대한 세부 정보는 로그 파일에 포함됩니다.  |
|`Corrupted`|데이터 수집 중에 계산된 CRC가 업로드 중에 계산된 CRC와 일치하지 않습니다.  |  
|`StorageRequestFailed` | Azure 스토리지 요청이 실패했습니다.   |     
|`LeasePresent` | 이 항목은 임대되었며 다른 사용자가 사용하고 있습니다. |
|`StorageRequestForbidden` |인증 문제로 인해 업로드할 수 없습니다. |
|`ManagedDiskCreationTerminalFailure` | 관리 디스크로 업로드할 수 없습니다. 파일은 준비 스토리지 계정에서 페이지 blob으로 사용할 수 있습니다. 페이지 blob을 관리 디스크로 수동으로 변환할 수 있습니다.  |
|`DiskConversionNotStartedTierInfoMissing` | VHD 파일이 미리 생성된 계층 폴더 외부에 복사되었으므로 관리 디스크가 생성되지 않았습니다. 파일이 주문 생성 중에 지정된 대로 준비 스토리지 계정에 페이지 blob으로 업로드됩니다. 관리 디스크로 수동 변환할 수 있습니다.|
|`InvalidWorkitem` | Azure 명명 및 제한 규칙에 맞지 않으므로 데이터를 업로드할 수 없습니다.|
|`InvalidPageBlobUploadAsBlockBlob` | 접두사 `databoxdisk-invalid-pb-`이 있는 컨테이너에서 블록 Blob으로 업로드됩니다.|
|`InvalidAzureFileUploadAsBlockBlob` | 접두사 `databoxdisk-invalid-af`-이 있는 컨테이너에서 블록 Blob으로 업로드됩니다.|
|`InvalidManagedDiskUploadAsBlockBlob` | 접두사 `databoxdisk-invalid-md`-이 있는 컨테이너에서 블록 Blob으로 업로드됩니다.|
|`InvalidManagedDiskUploadAsPageBlob` |접두사 `databoxdisk-invalid-md-`-이 있는 컨테이너에서 페이지 Blob으로 업로드됩니다. |
|`MovedToOverflowShare` |원래 공유 크기가 최대 Azure 크기 제한을 초과하여 새 공유에 업로드된 파일입니다. 새 파일 공유 이름에는 `-2` 접미사가 붙는 원래 이름이 있습니다.   |
|`MovedToDefaultAzureShare` |폴더의 일부가 아닌 파일을 기본 공유에 업로드했습니다. 공유 이름이 `databox-`로 시작합니다. |
|`ContainerRenamed` |이러한 파일의 컨테이너는 Azure 명명 규칙을 준수하지 않으며 이름이 바뀝니다. 새 이름은 `databox-`로 시작하고 원래 이름의 SHA1 해시 접미사가 붙습니다 |
|`ShareRenamed` |이 공유는 Azure 명명 규칙을 준수하지 않으며 이름이 변경되었습니다. 새 이름은 `databox-`로 시작하고 원래 이름의 SHA1 해시 접미사가 붙습니다. |
|`BlobRenamed` |이러한 파일은 Azure 명명 규칙을 준수하지 않으며 이름이 바뀌었습니다. `BlobPath` 필드에서 새 이름을 확인합니다. |
|`FileRenamed` |이러한 파일은 Azure 명명 규칙을 준수하지 않으며 이름이 바뀌었습니다. `FileStoragePath` 필드에서 새 이름을 확인합니다. |
|`DiskRenamed` |이러한 파일은 Azure 명명 규칙을 준수하지 않으며 이름이 바뀌었습니다. `BlobPath` 필드에서 새 이름을 확인합니다. |


## <a name="next-steps"></a>다음 단계

- [Data Box Disk 문제에 대한 지원 티켓을 엽니다](data-box-disk-contact-microsoft-support.md).
