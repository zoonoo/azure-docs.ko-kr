---
title: 로그를 사용하여 데이터 업로드 문제 해결
titleSuffix: Azure Data Box Disk
description: Azure Data Box Disk에 데이터를 업로드할 때 보이는 로그 및 문제 해결 문제를 사용하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7c14988706ef193ef5da868c55f6c4f55e7d98f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260138"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Azure 데이터 상자 디스크에서 데이터 업로드 문제를 해결하기 위해 로그 이해

이 문서는 Microsoft Azure 데이터 상자 디스크에 적용되며 Azure에 데이터를 업로드할 때 표시되는 문제를 설명합니다.

## <a name="about-upload-logs"></a>업로드 로그 소개

데이터가 데이터 센터의 Azure에 `_error.xml` 업로드되고 `_verbose.xml` 각 저장소 계정에 대해 파일이 생성되는 경우 이러한 로그는 데이터를 업로드하는 데 사용된 동일한 저장소 계정에 업로드됩니다. 

두 로그는 모두 동일한 형식이며 디스크에서 Azure Storage 계정으로 데이터를 복사하는 동안 발생한 이벤트에 대한 XML 설명을 포함합니다.

자세한 로그에는 모든 Blob 또는 파일에 대한 복사 작업 상태에 대한 전체 정보가 포함되어 있는 반면 오류 로그에는 업로드 중에 오류가 발생한 Blob 또는 파일에 대한 정보만 포함됩니다.

오류 로그는 자세한 로그와 동일한 구조를 가지고 있지만 성공적인 작업을 필터링합니다.

## <a name="download-logs"></a>로그 다운로드

다음 단계를 수행하여 업로드 로그를 찾습니다.

1. Azure에 데이터를 업로드할 때 오류가 있는 경우 포털은 진단 로그가 있는 폴더에 대한 경로를 표시합니다.

    ![포털의 로그 링크](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. **웨이즈로**이동 .

    ![오류 및 자세한 로그](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

각각의 경우 오류 로그와 자세한 로그가 표시됩니다. 각 로그를 선택하고 로컬 복사본을 다운로드합니다.

## <a name="sample-upload-logs"></a>샘플 업로드 로그

의 `_verbose.xml` 샘플은 아래와 같습니다. 이 경우 오류 없이 주문이 성공적으로 완료되었습니다.

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

동일한 순서의 `_error.xml` 샘플은 아래와 같습니다.

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

주문이 `_error.xml` 오류로 완료된 경우 의 샘플이 아래에 표시됩니다. 

이 경우 오류 파일에는 `Summary` 모든 파일 수준 오류가 포함된 섹션과 다른 섹션이 있습니다. 

과 `Summary` `CopyErrors`를 `ValidationErrors` 포함합니다. 이 경우 8개의 파일 또는 폴더가 Azure에 업로드되었으며 유효성 검사 오류가 없습니다. 데이터가 Azure Storage 계정으로 복사되면 5개의 파일 또는 폴더가 성공적으로 업로드됩니다. 나머지 3개의 파일 또는 폴더는 Azure 컨테이너 명명 규칙에 따라 이름이 변경된 다음 Azure에 성공적으로 업로드되었습니다.

파일 수준 상태는 `BlobStatus` Blob을 업로드하기 위해 수행된 모든 작업을 설명하는 것입니다. 이 경우 데이터가 복사된 폴더가 컨테이너에 대한 Azure 명명 규칙을 준수하지 않아 세 개의 컨테이너의 이름이 바뀝니다. 이러한 컨테이너에 업로드된 Blob의 경우 새 컨테이너 이름, Azure의 Blob 경로, 원래 잘못된 파일 경로 및 Blob 크기가 포함됩니다.
    
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

Azure에 데이터를 업로드할 때 생성된 오류는 다음 표에 요약되어 있습니다.

| 오류 코드 | 설명                   |
|-------------|------------------------------|
|`None` |  성공적으로 완료되었습니다.           |
|`Renamed` | Blob의 이름을 성공적으로 변경했습니다.   |
|`CompletedWithErrors` | 오류로 완료된 업로드. 오류가 있는 파일의 세부 정보가 로그 파일에 포함됩니다.  |
|`Corrupted`|데이터 수집 중에 계산된 CRC는 업로드 하는 동안 계산된 CRC와 일치하지 않습니다.  |  
|`StorageRequestFailed` | Azure 저장소 요청이 실패했습니다.   |     
|`LeasePresent` | 이 항목은 임대되며 다른 사용자가 사용하고 있습니다. |
|`StorageRequestForbidden` |인증 문제로 인해 업로드할 수 없습니다. |
|`ManagedDiskCreationTerminalFailure` | 관리 디스크로 업로드할 수 없습니다. 파일은 스테이징 저장소 계정에서 페이지 Blob로 사용할 수 있습니다. 페이지 Blob을 관리 디스크로 수동으로 변환할 수 있습니다.  |
|`DiskConversionNotStartedTierInfoMissing` | VHD 파일이 미리 생성된 계층 폴더 외부에서 복사되었기 때문에 관리디스크가 만들어지지 않았습니다. 파일은 주문 생성 중에 지정된 대로 스테이징 저장소 계정에 페이지 Blob로 업로드됩니다. 수동으로 관리 디스크로 변환할 수 있습니다.|
|`InvalidWorkitem` | Azure 명명 및 제한 규칙을 준수하지 않으므로 데이터를 업로드할 수 없습니다.|
|`InvalidPageBlobUploadAsBlockBlob` | 접두사가 `databoxdisk-invalid-pb-`있는 컨테이너에 블록 Blob로 업로드됩니다.|
|`InvalidAzureFileUploadAsBlockBlob` | 접두사가 `databoxdisk-invalid-af`있는 컨테이너에 블록 Blob로 업로드 -.|
|`InvalidManagedDiskUploadAsBlockBlob` | 접두사가 `databoxdisk-invalid-md`있는 컨테이너에 블록 Blob로 업로드 -.|
|`InvalidManagedDiskUploadAsPageBlob` |접두사가 `databoxdisk-invalid-md-`있는 컨테이너에 페이지 Blob로 업로드됩니다. |
|`MovedToOverflowShare` |원래 공유 크기가 최대 Azure 크기 제한을 초과함에 따라 새 공유에 업로드된 파일입니다. 새 파일 공유 이름에는 원래 이름이 `-2`함께 고정되어 있습니다.   |
|`MovedToDefaultAzureShare` |기본 공유에 폴더의 일부가 아닌 업로드된 파일입니다. 공유 이름은 에서 `databox-`시작합니다. |
|`ContainerRenamed` |이러한 파일의 컨테이너는 Azure 명명 규칙을 준수하지 않았으며 이름이 바뀝니다. 새 이름은 원래 `databox-` 이름의 SHA1 해시로 시작되고 접미사가 붙어 있습니다. |
|`ShareRenamed` |이러한 파일에 대한 공유는 Azure 명명 규칙을 준수하지 않았으며 이름이 바뀝니다. 새 이름은 원래 `databox-` 이름의 SHA1 해시로 시작되고 접미사가 붙어 있습니다. |
|`BlobRenamed` |이러한 파일은 Azure 명명 규칙을 준수하지 않았으며 이름이 바뀌었습니다. `BlobPath` 필드에 새 이름이 있는지 확인합니다. |
|`FileRenamed` |이러한 파일은 Azure 명명 규칙을 준수하지 않았으며 이름이 바뀌었습니다. `FileStoragePath` 필드에 새 이름이 있는지 확인합니다. |
|`DiskRenamed` |이러한 파일은 Azure 명명 규칙을 준수하지 않았으며 이름이 바뀌었습니다. `BlobPath` 필드에 새 이름이 있는지 확인합니다. |


## <a name="next-steps"></a>다음 단계

- [데이터 상자 디스크 문제에 대한 지원 티켓을 엽니다.](data-box-disk-contact-microsoft-support.md)
