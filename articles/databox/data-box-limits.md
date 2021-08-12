---
title: Azure Data Box 제한 | Microsoft Docs
description: Microsoft Azure Data Box 구성 요소 및 연결에 대한 시스템 제한 및 권장 크기를 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: 97d8da86565db73aa9a3866f39f793aaf0905470
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97900164"
---
# <a name="azure-data-box-limits"></a>Azure Data Box 제한

Microsoft Azure Data Box를 배포하고 운영할 때 이러한 제한을 고려합니다. 다음 표에서는 Data Box에 대한 이러한 제한을 설명합니다.

## <a name="data-box-service-limits"></a>Data Box 서비스 제한

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Data Box 제한

- Data Box는 가져오기 및 내보내기 모두에 대해 최대 5억 개의 파일을 저장할 수 있습니다.
- Data Box는 클라우드의 최대 512개 컨테이너 또는 공유를 지원합니다. 사용자 공유 내의 최상위 수준 디렉터리는 클라우드의 컨테이너 또는 Azure 파일 공유가 됩니다. 
- Data Box 사용량 용량은 ReFS 메타데이터 공간 소비로 인해 80TB 미만일 수 있습니다.
- Data Box는 NFS 공유에서 한 번에 최대 10개의 클라이언트 연결을 지원합니다.

## <a name="azure-storage-limits"></a>Azure Storage 제한

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>데이터 업로드 주의 사항


### <a name="for-import-order"></a>가져오기 주문의 경우

가져오기 주문에 대한 Data Box 주의 사항은 다음과 같습니다.

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

### <a name="for-export-order"></a>내보내기 주문의 경우

내보내기 주문에 대한 Data Box 주의 사항은 다음과 같습니다.

- Data Box는 Windows 기반 디바이스이며 대/소문자 구분 파일 이름을 지원하지 않습니다. 예를 들어, Azure에는 대/소문자만 다른 이름이 있는 두 개의 파일이 있을 수 있습니다. 디바이스에서 파일을 덮어쓰여지므로 Data Box를 사용하여 이러한 파일을 내보내지 마세요.
- 입력 파일에 중복된 태그가 있거나 동일한 데이터를 참조하는 태그가 있는 경우 Data Box 내보내기에서 파일을 건너뛰거나 덮어쓸 수 있습니다. Azure Portal이 표시하는 데이터의 파일 수와 크기는 디바이스의 실제 데이터 크기와 다를 수 있습니다. 
- Data Box는 SMB를 통해 Windows 기반 시스템으로 데이터를 내보내고 파일 및 폴더에 대한 SMB 제한에 따라 제한됩니다. 지원되지 않는 이름의 파일 및 폴더는 내보내지지 않습니다.
- 접두사에서 컨테이너로의 1:1 매핑이 있습니다.
- 최대 파일 이름 크기는 1024자입니다. 이 길이를 초과하는 파일 이름은 내보내지지 않습니다.
- *xml* 파일(주문을 만드는 동안 업로드됨)의 중복 접두사는 내보내집니다. 중복 접두사는 무시되지 않습니다.
- 페이지 Blob 및 컨테이너 이름은 대/소문자를 구분합니다. 대/소문자가 일치하지 않으면 Blob 및/또는 컨테이너가 검색되지 않습니다.
 

## <a name="azure-storage-account-size-limits"></a>Azure Storage 계정 크기 제한

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Azure 개체 크기 제한

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure 블록 Blob과 페이지 Blob 및 파일 명명 규칙

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
