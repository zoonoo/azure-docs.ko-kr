---
title: Azure Import/Export 서비스에 대한 요구 사항 | Microsoft Docs
description: Azure Import/Export 서비스에 대한 소프트웨어 및 하드웨어 요구 사항을 이해합니다.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: b6dffb46d8c0fd7201079de3e8509ece516d2f8f
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821401"
---
# <a name="azure-importexport-system-requirements"></a>Azure Import/Export에 대한 시스템 요구 사항

이 문서에서는 Azure Import/Export 서비스에 대한 중요한 요구 사항을 설명합니다. Import/Export 서비스를 사용하기 전에 정보를 신중하게 검토한 다음, 작업 중 필요에 따라 다시 검토하는 것이 좋습니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

WAImportExport 도구를 사용하여 하드 드라이브를 준비하기 위해 다음과 같은 **BitLocker 드라이브 암호화를 지원하는 64비트 OS**가 지원됩니다.


|플랫폼 |버전 |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Windows 클라이언트에 필요한 다른 소프트웨어

|플랫폼 |버전 |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>지원되는 스토리지 계정

Azure Import/Export 서비스는 다음과 같은 유형의 스토리지 계정을 지원합니다.

- 표준 범용 v2 저장소 계정 (대부분의 시나리오에 권장)
- Blob Storage 계정
- 범용 v1 스토리지 계정(클래식 또는 Azure Resource Manager 배포 모두), 

스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요.

각 작업은 하나의 스토리지 계정에서만 데이터 전송에 사용될 수 있습니다. 다시 말해, 하나의 가져오기/내보내기 작업이 여러 스토리지 계정에서 사용될 수 없습니다. 새 Storage 계정 만들기에 대한 자세한 내용은 [Storage 계정을 만드는 방법](storage-quickstart-create-account.md)(영문)을 참조하세요.

> [!IMPORTANT] 
> Azure Import Export 서비스는 [Virtual Network 서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md) 기능이 활성화된 스토리지 계정을 지원하지 않습니다. 

## <a name="supported-storage-types"></a>지원되는 스토리지 형식

다음 스토리지 유형 목록은 Azure Import/Export 서비스를 지원합니다.


|작업  |스토리지 서비스 |지원됨  |지원하지 않음  |
|---------|---------|---------|---------|
|가져오기     |  Azure Blob Storage <br><br> Azure File Storage       | 블록 Blob 및 페이지 Blob 지원됨 <br><br> 파일 지원됨          |
|내보내기     |   Azure Blob Storage       | 블록 Blob, 페이지 Blob 및 추가 Blob 지원됨         | Azure Files 지원되지 않음


## <a name="supported-hardware"></a>지원되는 하드웨어 

Azure Import/Export 서비스의 경우 데이터를 복사할 지원되는 디스크가 필요합니다.

### <a name="supported-disks"></a>지원되는 디스크

다음 디스크 목록은 Import/Export 서비스에서 사용하도록 지원됩니다.


|디스크 유형  |크기  |지원됨 |
|---------|---------|---------|
|SSD    |   2.5"      |SATA III          | 
|HDD     |  2.5"<br>3.5"       |SATA II, SATA III         |

지원 되지 않는 디스크 유형은 다음과 같습니다.
- USBs.
- 기본 제공 USB 어댑터를 사용 하는 외부 HDD.
- 외부 HDD의 대/소문자를 포함 하는 디스크입니다.

단일 가져오기/내보내기 작업은 다음을 포함합니다.
- 최대 10개의 HDD/SSD.
- 모든 규모의 HDD/SSD 혼합.

다수의 드라이브가 여러 작업에 분산될 수 있으며, 만들 수 있는 작업 수에 대한 제한은 없습니다. 가져오기 작업의 경우 드라이브의 첫 번째 데이터 볼륨만 처리됩니다. 데이터 볼륨은 NTFS로 포맷되어 있어야 합니다.

하드 드라이브를 준비하고 WAImportExport 도구를 사용하여 데이터를 복사하는 경우, 외부 USB 어댑터를 사용할 수 있습니다. 대부분의 기성품 USB 3.0 이상 어댑터가 작동해야 합니다. 


## <a name="next-steps"></a>다음 단계

* [WAImportExport 도구 설정](storage-import-export-tool-how-to.md)
* [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)
* [Azure Import/Export REST API 샘플](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)

