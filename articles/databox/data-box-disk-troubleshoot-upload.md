---
title: Azure Data Box 디스크 문제 해결 데이터 로그를 사용 하 여 문제를 업로드 | Microsoft Docs
description: 로그를 사용 하 여 Azure Data Box 디스크에 데이터를 업로드할 때 나타나는 문제를 해결 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 6f3ac38c3eac968bd2f7ec2aada435466d3ff279
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148310"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Azure Data Box 디스크에 데이터 업로드 문제를 해결 하기 위해 로그 이해

이 문서에서는 Microsoft Azure Data Box 디스크에 적용 되며 Azure에 데이터를 업로드할 때를 표시 하는 문제에 설명 합니다.

## <a name="data-upload-logs"></a>로그를 업로드 하는 데이터

데이터는 데이터 센터에서 Azure에 업로드 되 면 `_error.xml` 고 `_verbose.xml` 파일이 생성 됩니다. 이러한 로그는 데이터를 업로드 하는 데 사용 된 동일한 저장소 계정에 업로드 됩니다. 샘플은 `_error.xml` 아래에 표시 됩니다.
    
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

## <a name="download-logs"></a>로그 다운로드

찾기 및 진단 로그를 다운로드 하는 방법은 두 가지입니다.

- 오류가 있는 경우 모든 데이터를 Azure에 업로드 하는 경우, 포털 진단 로그가 위치한 폴더로 경로 표시 합니다.

    ![포털에서 로그에 연결](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

- Data Box 주문 연관 된 저장소 계정으로 이동 합니다. **Blob service > Blob 찾아보기**로 이동하고 저장소 계정에 해당하는 Blob을 찾습니다. 로 이동 **waies**합니다.

    ![복사 로그 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

각각의 경우 오류 로그 및 자세한 로그를 볼 수 있습니다. 각 로그를 선택 하 고 로컬 복사본을 다운로드 합니다.


## <a name="data-upload-errors"></a>데이터 업로드 오류

Azure에 데이터를 업로드 하는 경우 생성 된 오류는 다음 표에 요약 되어 있습니다.

| 오류 코드 | 설명                        |
|-------------|------------------------------|
|`None` |  완료 되었습니다.           |
|`Renamed` | Blob을 이름을 바꾸었습니다.  |                                                            |
|`CompletedWithErrors` | 업로드를 완료 했지만 오류가 발생 합니다. 오류 파일의 세부 정보는 로그 파일에 포함 됩니다.  |
|`Corrupted`|데이터 수집 중에 계산 된 CRC 업로드 중에 계산 된 CRC 일치 하지 않습니다.  |  
|`StorageRequestFailed` | Azure 저장소 요청이 실패 했습니다.   |     |
|`LeasePresent` | 이 항목이 임대 및 다른 사용자가 사용 됩니다. |
|`StorageRequestForbidden` |인증 문제로 인해 업로드 하지 못했습니다. |
|`ManagedDiskCreationTerminalFailure` | 관리 되는 디스크로 업로드할 수 없습니다. 페이지 blob으로 파일은 준비 저장소 계정에서 사용할 수 있습니다. Managed disks로 페이지 blob을 수동으로 변환할 수 있습니다.  |
|`DiskConversionNotStartedTierInfoMissing` | VHD 파일을 복사 하 여 precreated 계층 폴더 외부에 있으므로 관리 디스크 생성 되지 않았습니다. 파일 순서를 만드는 동안 지정 된 대로 준비 저장소 계정에 페이지 blob으로 업로드 됩니다. 관리 디스크를 수동으로 변환할 수 있습니다.|
|`InvalidWorkitem` | Azure 명명에 맞지 않습니다 하 고 규칙 제한에 데이터를 업로드 하지 못했습니다.|
|`InvalidPageBlobUploadAsBlockBlob` | 접두사를 사용 하 여 컨테이너에 블록 blob으로 업로드 `databoxdisk-invalid-pb-`합니다.|
|`InvalidAzureFileUploadAsBlockBlob` | 접두사를 사용 하 여 컨테이너에 블록 blob으로 업로드 `databoxdisk-invalid-af`-합니다.|
|`InvalidManagedDiskUploadAsBlockBlob` | 접두사를 사용 하 여 컨테이너에 블록 blob으로 업로드 `databoxdisk-invalid-md`-합니다.|
|`InvalidManagedDiskUploadAsPageBlob` |접두사를 사용 하 여 컨테이너에 페이지 blob으로 업로드 `databoxdisk-invalid-md-`합니다. |
|`MovedToOverflowShare` |원래 공유 크기와 새 공유로 업로드 된 파일에는 최대 Azure 크기 제한을 초과 했습니다. 새 파일 공유 이름에 원래 이름을 붙음 `-2`합니다.   |
|`MovedToDefaultAzureShare` |기본 공유 폴더에 속하지 않았던 업로드 된 파일입니다. 공유 이름은 시작 `databox-`합니다. |
|`ContainerRenamed` |이러한 파일의 컨테이너는 Azure 명명 규칙을 준수 하지 않은 하 고 이름이 바뀝니다. 새 이름을 사용 하 여 시작 `databox-` 원래 이름의 SHA1 해시 붙습니다 및 |
|`ShareRenamed` |이러한 파일에 대 한 공유 Azure 명명 규칙을 준수 하지 않은 하 고 이름이 바뀝니다. 새 이름을 사용 하 여 시작 `databox-` 및 원래 이름의 SHA1 해시 붙습니다. |
|`BlobRenamed` |이러한 Azure 명명 규칙을 준수 하지 않은 파일과 이름이 바뀌었습니다. 확인 된 `BlobPath` 새 이름에 대 한 필드입니다. |
|`FileRenamed` |이러한 Azure 명명 규칙을 준수 하지 않은 파일과 이름이 바뀌었습니다. 확인 된 `FileStoragePath` 새 이름에 대 한 필드입니다. |
|`DiskRenamed` |이러한 Azure 명명 규칙을 준수 하지 않은 파일과 이름이 바뀌었습니다. 확인 된 `BlobPath` 새 이름에 대 한 필드입니다. |


## <a name="next-steps"></a>다음 단계

- [Data Box 디스크 문제에 대 한 지원 티켓을 열어](data-box-disk-contact-microsoft-support.md)합니다.
