---
title: Azure Data Box 제한 | Microsoft Docs
description: Microsoft Azure Data Box 구성 요소 및 연결에 대한 시스템 제한 및 권장 크기를 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/22/2019
ms.author: alkohli
ms.openlocfilehash: 147cf61dcd36edc75a936cf9b467fd89c8d8a965
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60326601"
---
# <a name="azure-data-box-limits"></a>Azure Data Box 제한

Microsoft Azure Data Box를 배포하고 운영할 때 이러한 제한을 고려합니다. 다음 표에서는 Data Box에 대한 이러한 제한을 설명합니다.


## <a name="data-box-service-limits"></a>Data Box 서비스 제한

 - Data Box 서비스를 사용 하 여 저장소 계정을 여러 개를 사용 하는 경우 모든 저장소 계정을 동일한 Azure 지역에 속해야 합니다.
 - 세 개 이하의 저장소 계정을 사용하는 것이 좋습니다. 더 많은 저장소 계정을 사용 하 여 성능에 잠재적으로 영향을 줄 수입니다.

## <a name="data-box-limits"></a>Data Box 제한

- Data Box는 최대 5억 개의 파일을 저장할 수 있습니다.

## <a name="azure-storage-limits"></a>Azure Storage 제한

이 섹션에서는 Azure Storage 서비스에 대한 제한 및 Azure Files, Azure 블록 Blob 및 Azure 페이지 Blob에 대한 필수 명명 규칙을 Data Box 서비스에 적용 가능한 것으로 설명합니다. 저장소 제한을 신중히 검토하고 모든 권장 사항을 수행합니다.

Azure 저장소 서비스 제한에 대한 최신 정보 및 공유, 컨테이너 및 파일 이름 지정에 대한 모범 사례는 다음으로 이동합니다.

- [컨테이너 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [공유 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [블록 Blob 및 페이지 Blob 규칙](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> 가 모든 파일이 나 디렉터리 또는 Azure Storage 서비스 제한을 초과 하 여 Azure 파일/Blob 명명 규칙을 따르지는 이러한 파일 또는 디렉터리가 Data Box 서비스를 통해 Azure Storage에 수집 되지 않습니다.

## <a name="data-upload-caveats"></a>데이터 업로드 주의 사항

- Precreated 공유 중 하나에 직접 파일을 복사 하지 마십시오. 공유에서 폴더를 만들고 해당 폴더에 파일을 복사 해야 합니다.
- *StorageAccount_BlockBlob* 및 *StorageAccount_PageBlob*의 폴더는 컨테이너입니다. 예를 들어 컨테이너는 *StorageAccount_BlockBlob/container* 및 *StorageAccount_PageBlob/container*로 만들어집니다.
- *StorageAccount_AzureFiles*에서 직접 만들어진 각각의 폴더는 Azure 파일 공유로 변환됩니다.
- 복사되는 개체와 동일한 이름을 사용하는 기존 Azure 개체(Blob 또는 파일)가 클라우드에 있는 경우, Data Box가 클라우드의 파일을 덮어씁니다.
- *StorageAccount_BlockBlob* 및 *StorageAccount_PageBlob* 공유에 기록된 모든 파일은 각각 블록 Blob 및 페이지 Blob으로 업로드됩니다.
- Azure blob 저장소 디렉터리를 지원 하지 않습니다. *StorageAccount_BlockBlob* 폴더에 폴더를 만든 경우, 가상 폴더가 Blob 이름으로 생성됩니다. Azure Files을 위한 실제 디렉터리 구조가 유지됩니다.
- 모든 빈 만들어지는 디렉터리 계층 구조 (파일) 없이 *StorageAccount_BlockBlob* 하 고 *StorageAccount_PageBlob* 폴더 업로드 되지 않습니다.
- 데이터를 Azure에 업로드할 때 오류가 발생하는 경우 오류 로그는 대상 저장소 계정에서 만들어집니다. 업로드가 완료되면 오류 로그 경로를 사용할 수 있으며, 로그를 검토하여 정정 작업을 수행할 수 있습니다. 업로드 된 데이터를 확인 하지 않고 원본에서 데이터를 삭제 하지 마십시오.

## <a name="azure-storage-account-size-limits"></a>Azure Storage 계정 크기 제한

저장소 계정에 복사되는 데이터 크기에 대한 제한은 다음과 같습니다. 업로드한 데이터가 이러한 제한을 준수하는지 확인합니다. 이러한 제한에 대한 최신 정보는 [Azure Blob Storage 크기 조정 목표](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) 및 [Azure Files 크기 조정 목표](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets)로 이동합니다.

| Azure 저장소 계정에 복사되는 데이터의 크기                      | 기본 제한          |
|---------------------------------------------------------------------|------------------------|
| 블록 Blob 및 페이지 Blob                                            | 저장소 계정당 500TiB. <br> Data Box를 비롯한 모든 원본 데이터를 포함합니다.|
| Azure 파일                                                          | 공유당 5TiB.<br> *StorageAccount_AzureFiles*의 모든 폴더는 이 제한을 따라야 합니다.       |

## <a name="azure-object-size-limits"></a>Azure 개체 크기 제한

쓸 수 있는 Azure 개체의 크기는 다음과 같습니다. 업로드되는 모든 파일이 이러한 제한을 준수하는지 확인합니다.

| Azure 개체 형식 | 기본 제한                                             |
|-------------------|-----------------------------------------------------------|
| 블록 Blob        | ~ 4.75TiB                                                 |
| 페이지 Blob         | 8TiB <br> 페이지 Blob 형식으로 업로드되는 모든 파일은 정렬된 512바이트(정수의 배수)여야 하며, 그렇지 않은 경우 업로드되지 않습니다. <br> VHD 및 VHDX는 정렬된 512바이트입니다. |
| Azure 파일        | 1TiB                                                      |
| 관리 디스크     | 4TiB <br> 크기 제한에 대 한 자세한 내용은 다음을 참조 하세요. <li>[표준 Ssd의 확장성 목표](../virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Premium Ssd의 확장성 목표](../virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[표준 Hdd의 확장성 목표](../virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[가격 책정 및 청구의 managed disks](../virtual-machines/windows/disks-types.md#billing)</li>                                                     |

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure 블록 Blob과 페이지 Blob 및 파일 명명 규칙

| 엔터티                                       | 규칙                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 블록 Blob 및 페이지 Blob에 대한 컨테이너 이름 | 올바른 DNS 이름은 3~63자여야 합니다. <br>  문자 또는 숫자로 시작해야 합니다. <br> 소문자, 숫자 및 하이픈(-)만 포함할 수 있습니다. <br> 모든 하이픈(-)은 앞뒤에 문자 또는 숫자가 와야 합니다. <br> 이름에 하이픈을 연속으로 허용 되지 않습니다. |
| Azure 파일에 대한 공유 이름                  | 위와 동일                                                                                                                                                                                                                                                                                                             |
| Azure 파일에 대한 디렉터리와 파일 이름     |<li> 대/소문자 구분, 대/소문자를 구분하지 않으며 길이가 255자를 초과할 수 없습니다. </li><li> 슬래시(/)로 끝낼 수 없습니다. </li><li>사용된 경우 자동으로 제거됩니다. </li><li> 다음 문자는 허용 되지 않습니다. <code>" \\ / : \| < > * ?</code></li><li> 예약된 URL 문자는 적절히 이스케이프되어야 합니다. </li><li> 잘못 된 URL 경로 문자는 허용 되지 않습니다. 과 같은 코드 포인트 \\uE000 유효한 유니코드 문자가 없습니다. 일부 ASCII 또는 유니코드 문자 같은 제어 문자 ((0x00-0x1f, \\u0081, 등), 허용 되지 않습니다. HTTP/1.1에서 유니코드 문자열을 제어하는 규칙은 RFC 2616, 섹션 2.2: 기본 규칙 및 RFC 3987을 참조하세요. </li><li> 다음 파일 이름은 허용 되지 않습니다. LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, 점 문자(.) 및 2개 점 문자(..)</li>|
| 블록 Blob 및 페이지 Blob에 대한 Blob 이름      | </li><li>Blob 이름은 대/소문자를 구분하며 문자 조합을 포함할 수 있습니다. </li><li>Blob 이름은 길이가 1~1,024자 사이여야 합니다. </li><li>예약된 URL 문자는 적절히 이스케이프되어야 합니다. </li><li>Blob 이름을 구성하는 경로 세그먼트 수는 254개를 초과할 수 없습니다. 경로 세그먼트는 가상 디렉터리 이름에 해당하는 연속 구분 기호 문자 사이의 문자열입니다(예를 들어 슬래시 '/').</li> |
