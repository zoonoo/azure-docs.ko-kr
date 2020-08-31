---
title: Azure Data Box Disk 문제 해결 | Microsoft Docs
description: 로그를 사용 하 여 Azure Data Box Disk를 배포할 때 발생할 수 있는 유효성 검사 문제를 해결 하는 방법에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 1c1b38c4021660b9f59098f8442d16bfd0ecc582
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87925545"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>로그를 사용 하 여 Azure Data Box Disk 유효성 검사 문제 해결

이 문서는 Microsoft Azure Data Box Disk에 적용 됩니다. 이 문서에서는 로그를 사용 하 여이 솔루션을 배포할 때 볼 수 있는 유효성 검사 문제를 해결 하는 방법을 설명 합니다.

## <a name="validation-tool-log-files"></a>유효성 검사 도구 로그 파일

[유효성 검사 도구](data-box-disk-deploy-copy-data.md#validate-data)를 사용 하 여 디스크에서 데이터의 유효성을 검사 하는 경우 오류를 기록 하는 *error.xml* 생성 됩니다. 로그 파일은 `Drive:\DataBoxDiskImport\logs` 드라이브의 폴더에 있습니다. 유효성 검사를 실행할 때 오류 로그에 대 한 링크가 제공 됩니다.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

유효성 검사를 위해 여러 세션을 실행 하는 경우 세션당 하나의 오류 로그가 생성 됩니다.

- 다음은 폴더에 로드 된 데이터가 `PageBlob` 512 바이트로 정렬 되지 않은 경우의 오류 로그 샘플입니다. PageBlob에 업로드 된 데이터는 512 바이트 정렬 되어야 합니다 (예: VHD 또는 VHDX). 이 파일의 오류는의 `<Errors>` 및 경고에 있습니다 `<Warnings>` .

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

- 다음은 컨테이너 이름이 유효 하지 않은 경우의 오류 로그 샘플입니다. `BlockBlob`디스크의, 또는 폴더 아래에 만든 폴더는 `PageBlob` `AzureFile` Azure Storage 계정의 컨테이너가 됩니다. 컨테이너 이름은 [Azure 명명 규칙](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)을 따라야 합니다.

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

*error.xml* 에 포함 된 오류는 다음 표에 요약 되어 있습니다.

| 오류 코드| Description                       | 권장 작업               |
|------------|--------------------------|-----------------------------------|
| `None` | 데이터의 유효성을 검사 했습니다. | 사용자가 조치할 필요는 없습니다. |
| `InvalidXmlCharsInPath` |파일 경로에 잘못 된 문자가 있기 때문에 매니페스트 파일을 만들 수 없습니다. | 계속 하려면 이러한 문자를 제거 합니다.  |
| `OpenFileForReadFailed`| 파일을 처리할 수 없습니다. 이는 액세스 문제나 파일 시스템이 손상 되었기 때문일 수 있습니다.|오류로 인해 파일을 읽을 수 없습니다. 오류 세부 정보는 예외에 있습니다. |
| `Not512Aligned` | 이 파일은 PageBlob 폴더에 대해 올바른 형식이 아닙니다.| 512 바이트를 폴더에 맞춘 데이터만 업로드 `PageBlob` 합니다. PageBlob 폴더에서 파일을 제거 하거나 BlockBlob 폴더로 이동 합니다. 유효성 검사를 다시 시도 합니다.|
| `InvalidBlobPath` | Azure Blob 명명 규칙에 따라 파일 경로가 클라우드의 올바른 blob 경로에 매핑되지 않습니다.|Azure 명명 지침에 따라 파일 경로의 이름을 바꿉니다. |
| `EnumerationError` | 유효성 검사를 위해 파일을 열거할 수 없습니다. |이 오류가 발생 하는 이유는 여러 가지가 있을 수 있습니다. 가장 가능성이 높은 이유는 파일에 대 한 액세스입니다. |
| `ShareSizeExceeded` | 이 파일을 통해 Azure 파일 공유 크기가 Azure 제한인 5TB를 초과 합니다.|[Azure 개체 크기 제한을](data-box-disk-limits.md#azure-object-size-limits)따르도록 공유의 데이터 크기를 줄입니다. 유효성 검사를 다시 시도 합니다. |
| `AzureFileSizeExceeded` | 파일 크기가 Azure 파일 크기 제한을 초과 합니다.| [Azure 개체 크기 제한을](data-box-disk-limits.md#azure-object-size-limits)따르도록 파일 또는 데이터의 크기를 줄입니다. 유효성 검사를 다시 시도 합니다.|
| `BlockBlobSizeExceeded` | 파일 크기가 Azure 블록 Blob 크기 제한을 초과 합니다. | [Azure 개체 크기 제한을](data-box-disk-limits.md#azure-object-size-limits)따르도록 파일 또는 데이터의 크기를 줄입니다. 유효성 검사를 다시 시도 합니다. |
| `ManagedDiskSizeExceeded` | 파일 크기가 Azure 관리 디스크 크기 제한을 초과 합니다. | [Azure 개체 크기 제한을](data-box-disk-limits.md#azure-object-size-limits)따르도록 파일 또는 데이터의 크기를 줄입니다. 유효성 검사를 다시 시도 합니다. |
| `PageBlobSizeExceeded` | 파일 크기가 Azure 관리 디스크 크기 제한을 초과 합니다. | [Azure 개체 크기 제한을](data-box-disk-limits.md#azure-object-size-limits)따르도록 파일 또는 데이터의 크기를 줄입니다. 유효성 검사를 다시 시도 합니다. |
| `InvalidShareContainerFormat`  |디렉터리 이름이 컨테이너 또는 공유의 Azure 명명 규칙을 따르지 않습니다.         |디스크의 기존 폴더에서 만든 첫 번째 폴더는 저장소 계정의 컨테이너가 됩니다. 이 공유 또는 컨테이너 이름이 Azure 명명 규칙을 따르지 않습니다. [Azure 명명 규칙](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)을 따르도록 파일의 이름을 바꿉니다. 유효성 검사를 다시 시도 합니다.   |
| `InvalidBlobNameFormat` | Azure Blob 명명 규칙에 따라 파일 경로가 클라우드의 올바른 blob 경로에 매핑되지 않습니다.|[Azure 명명 규칙](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)을 따르도록 파일의 이름을 바꿉니다. 유효성 검사를 다시 시도 합니다. |
| `InvalidFileNameFormat` | 파일 경로는 Azure 파일 명명 규칙에 따라 클라우드의 올바른 파일 경로에 매핑되지 않습니다. |[Azure 명명 규칙](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)을 따르도록 파일의 이름을 바꿉니다. 유효성 검사를 다시 시도 합니다. |
| `InvalidDiskNameFormat` | Azure Managed Disk 명명 규칙에 따라 파일 경로가 클라우드의 올바른 디스크 이름에 매핑되지 않습니다. |[Azure 명명 규칙](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)을 따르도록 파일의 이름을 바꿉니다. 유효성 검사를 다시 시도 합니다.       |
| `NotPartOfFileShare` | 파일의 업로드 경로가 잘못 되었습니다. Azure Files의 폴더에 파일을 업로드 합니다.   | 오류가 발생 한 파일을 제거 하 고 미리 만든 폴더에 파일을 업로드 합니다. 유효성 검사를 다시 시도 합니다. |
| `NonVhdFileNotSupportedForManagedDisk` | 비-VHD 파일은 관리 디스크로 업로드할 수 없습니다. |파일이 지원 되지 않으므로 폴더에서 비 VHD 파일을 제거 `ManagedDisk` 하거나 `PageBlob` 폴더로 이동 하십시오. 유효성 검사를 다시 시도 합니다. |


## <a name="next-steps"></a>다음 단계

- [데이터 업로드 오류](data-box-disk-troubleshoot-upload.md)문제를 해결 합니다.
