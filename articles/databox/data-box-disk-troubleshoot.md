---
title: Azure Data Box Disk 문제 해결 | Microsoft Docs
description: Azure Data Box Disk에서 발견된 문제 해결 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67805717"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>로그를 사용하여 Azure 데이터 박스 디스크의 유효성 검사 문제 해결

이 문서는 Microsoft Azure 데이터 상자 디스크에 적용됩니다. 이 문서에서는 로그를 사용하여 이 솔루션을 배포할 때 볼 수 있는 유효성 검사 문제를 해결하는 방법을 설명합니다.

## <a name="validation-tool-log-files"></a>유효성 검사 도구 로그 파일

[유효성 검사 도구를](data-box-disk-deploy-copy-data.md#validate-data)사용하여 디스크의 데이터를 확인하면 오류를 기록하기 위해 *error.xml이* 생성됩니다. 로그 파일은 드라이브의 폴더에 `Drive:\DataBoxDiskImport\logs` 있습니다. 유효성 검사를 실행할 때 오류 로그에 대한 링크가 제공됩니다.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

유효성 검사를 위해 여러 세션을 실행하는 경우 세션당 하나의 오류 로그가 생성됩니다.

- 다음은 `PageBlob` 폴더에 로드된 데이터가 512바이트가 정렬되지 않은 오류 로그의 샘플입니다. PageBlob에 업로드된 모든 데이터는 512바이트(예: VHD 또는 VHDX)여야 합니다. 이 파일의 오류는 `<Errors>` 의 및 `<Warnings>`경고에 있습니다.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- 다음은 컨테이너 이름이 유효하지 않은 오류 로그의 샘플입니다. 에서 만들거나 `BlockBlob` `PageBlob` `AzureFile` 디스크의 폴더가 Azure Storage 계정의 컨테이너가 됩니다. 컨테이너 이름은 Azure 명명 규칙을 따라야 [합니다.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>유효성 검사 도구 오류

*error.xml에* 포함된 오류와 해당 권장 작업이 다음 표에 요약되어 있습니다.

| 오류 코드| 설명                       | 권장 작업               |
|------------|--------------------------|-----------------------------------|
| `None` | 데이터의 유효성을 검사했습니다. | 사용자가 조치할 필요는 없습니다. |
| `InvalidXmlCharsInPath` |파일 경로에 유효하지 않은 문자가 있기 때문에 매니페스트 파일을 만들 수 없습니다. | 이러한 문자를 제거하여 진행합니다.  |
| `OpenFileForReadFailed`| 파일을 처리할 수 없습니다. 액세스 문제 또는 파일 시스템 손상으로 인해 발생할 수 있습니다.|오류로 인해 파일을 읽을 수 없습니다. 오류 세부 정보는 예외입니다. |
| `Not512Aligned` | 이 파일은 PageBlob 폴더에 대 한 유효한 형식이 아닙니다.| 폴더에 정렬된 512바이트의 데이터만 업로드합니다. `PageBlob` PageBlob 폴더에서 파일을 제거하거나 BlockBlob 폴더로 이동합니다. 유효성 검사를 다시 시도합니다.|
| `InvalidBlobPath` | 파일 경로Azure Blob 명명 규칙에 따라 클라우드에서 유효한 Blob 경로에 매핑 되지 않습니다.|Azure 이름 지정 지침을 따라 파일 경로의 이름을 바꿉니다. |
| `EnumerationError` | 유효성 검사를 위해 파일을 등록할 수 없습니다. |이 오류에는 여러 가지 이유가 있을 수 있습니다. 가장 큰 이유는 파일에 대한 액세스입니다. |
| `ShareSizeExceeded` | 이 파일로 인해 Azure 파일 공유 크기가 Azure 제한인 5TB를 초과했습니다.|Azure 개체 크기 제한을 준수하도록 공유의 데이터 [크기를](data-box-disk-limits.md#azure-object-size-limits)줄입니다. 유효성 검사를 다시 시도합니다. |
| `AzureFileSizeExceeded` | 파일 크기가 Azure 파일 크기 제한을 초과합니다.| [Azure 개체](data-box-disk-limits.md#azure-object-size-limits)크기 제한을 준수하도록 파일 또는 데이터의 크기를 줄입니다. 유효성 검사를 다시 시도합니다.|
| `BlockBlobSizeExceeded` | 파일 크기가 Azure 블록 Blob 크기 제한을 초과합니다. | [Azure 개체](data-box-disk-limits.md#azure-object-size-limits)크기 제한을 준수하도록 파일 또는 데이터의 크기를 줄입니다. 유효성 검사를 다시 시도합니다. |
| `ManagedDiskSizeExceeded` | 파일 크기가 Azure 관리 디스크 크기 제한을 초과합니다. | [Azure 개체](data-box-disk-limits.md#azure-object-size-limits)크기 제한을 준수하도록 파일 또는 데이터의 크기를 줄입니다. 유효성 검사를 다시 시도합니다. |
| `PageBlobSizeExceeded` | 파일 크기가 Azure 관리 디스크 크기 제한을 초과합니다. | [Azure 개체](data-box-disk-limits.md#azure-object-size-limits)크기 제한을 준수하도록 파일 또는 데이터의 크기를 줄입니다. 유효성 검사를 다시 시도합니다. |
| `InvalidShareContainerFormat`  |디렉터리 이름은 컨테이너 또는 공유에 대한 Azure 명명 규칙을 준수하지 않습니다.         |디스크의 기존 폴더 아래에 생성된 첫 번째 폴더는 저장소 계정의 컨테이너가 됩니다. 이 공유 또는 컨테이너 이름은 Azure 명명 규칙을 따르지 않습니다. Azure 명명 규칙을 준수하도록 파일 이름을 [바꿉니다.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) 유효성 검사를 다시 시도합니다.   |
| `InvalidBlobNameFormat` | 파일 경로Azure Blob 명명 규칙에 따라 클라우드에서 유효한 Blob 경로에 매핑 되지 않습니다.|Azure 명명 규칙을 준수하도록 파일 이름을 [바꿉니다.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) 유효성 검사를 다시 시도합니다. |
| `InvalidFileNameFormat` | 파일 경로Azure File 명명 규칙에 따라 클라우드에서 유효한 파일 경로에 매핑되지 않습니다. |Azure 명명 규칙을 준수하도록 파일 이름을 [바꿉니다.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) 유효성 검사를 다시 시도합니다. |
| `InvalidDiskNameFormat` | 파일 경로는 Azure 관리 디스크 명명 규칙에 따라 클라우드에서 유효한 디스크 이름에 매핑되지 않습니다. |Azure 명명 규칙을 준수하도록 파일 이름을 [바꿉니다.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) 유효성 검사를 다시 시도합니다.       |
| `NotPartOfFileShare` | 파일의 업로드 경로가 잘못되었습니다. Azure 파일의 폴더에 파일을 업로드합니다.   | 오류가 있는 파일을 제거하고 해당 파일을 미리 만든 폴더에 업로드합니다. 유효성 검사를 다시 시도합니다. |
| `NonVhdFileNotSupportedForManagedDisk` | VHD가 아닌 파일은 관리 디스크로 업로드할 수 없습니다. |지원되지 않는 경우 VHD가 아닌 파일을 폴더에서 `ManagedDisk` 제거하거나 `PageBlob` 폴더로 이동합니다. 유효성 검사를 다시 시도합니다. |


## <a name="next-steps"></a>다음 단계

- 데이터 [업로드 오류](data-box-disk-troubleshoot-upload.md)문제 해결 .
