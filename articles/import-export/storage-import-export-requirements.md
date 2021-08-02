---
title: Azure Import/Export 서비스에 대한 요구 사항 | Microsoft Docs
description: Azure Import/Export 서비스에 대한 소프트웨어 및 하드웨어 요구 사항을 이해합니다.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 04/28/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: e766b2aba1aef47b16b4351c7852bb2f457475fa
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111887603"
---
# <a name="azure-importexport-system-requirements"></a>Azure Import/Export에 대한 시스템 요구 사항

이 문서에서는 Azure Import/Export 서비스에 대한 중요한 요구 사항을 설명합니다. Import/Export 서비스를 사용하기 전에 정보를 신중하게 검토한 다음, 작업 중 필요에 따라 다시 검토하는 것이 좋습니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

WAImportExport 도구를 사용하여 하드 드라이브를 준비하기 위해 다음과 같은 **BitLocker 드라이브 암호화를 지원하는 64비트 OS** 가 지원됩니다.


|플랫폼 |버전 |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> 윈도우 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Windows 클라이언트에 필요한 다른 소프트웨어

|플랫폼 |버전 |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>지원되는 스토리지 계정

Azure Import/Export 서비스는 다음과 같은 유형의 스토리지 계정을 지원합니다.

- 표준 범용 v2 스토리지 계정(대부분의 시나리오에 권장됨)
- Blob Storage 계정
- 범용 v1 스토리지 계정(클래식 또는 Azure Resource Manager 모두)

> [!IMPORTANT]
> Azure Blob Storage의 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원은 Azure Import/Export에서 지원되지 않습니다.

스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](../storage/common/storage-account-overview.md)를 참조하세요.

각 작업은 하나의 스토리지 계정에서만 데이터 전송에 사용될 수 있습니다. 다시 말해, 하나의 가져오기/내보내기 작업이 여러 스토리지 계정에서 사용될 수 없습니다. 새 Storage 계정 만들기에 대한 자세한 내용은 [Storage 계정을 만드는 방법](../storage/common/storage-account-create.md)(영문)을 참조하세요.

> [!IMPORTANT]
> [가상 네트워크 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md) 기능을 사용하도록 설정된 스토리지 계정의 경우 **신뢰할 수 있는 Microsoft 서비스 허용...** 설정을 사용하여 [Import/Export 사용](../storage/common/storage-network-security.md) 서비스에서 Azure로/에서 데이터 Import/Export를 수행할 수 있도록 합니다.

## <a name="supported-storage-types"></a>지원되는 스토리지 형식

다음 스토리지 유형 목록은 Azure Import/Export 서비스를 지원합니다.


|작업  |스토리지 서비스 |지원됨  |지원되지 않음  |
|---------|---------|---------|---------|
|가져오기     |  Azure Blob Storage <br><br> Azure Files 스토리지       | 블록 Blob 및 페이지 Blob 지원됨 <br><br> 파일 지원됨          |
|내보내기     |   Azure Blob Storage       | 블록 Blob, 페이지 Blob 및 추가 Blob 지원됨         | Azure Files 지원되지 않음<br>보관 계층에서 내보내기 지원되지 않음|


## <a name="supported-hardware"></a>지원되는 하드웨어

Azure Import/Export 서비스의 경우 데이터를 복사할 지원되는 디스크가 필요합니다.

### <a name="supported-disks"></a>지원되는 디스크

다음 디스크 목록은 Import/Export 서비스에서 사용하도록 지원됩니다.


|디스크 유형  |크기  |지원됨 |
|---------|---------|---------|
|SSD    |   2.5"      |SATA III          |
|HDD     |  2.5"<br>3.5"       |SATA II, SATA III         |

다음 디스크 형식은 지원되지 않습니다.

- USBs.
- 외부 HDD(기본 제공 USB 어댑터 포함)
- 외부 HDD 케이싱 내 디스크

단일 가져오기/내보내기 작업은 다음을 포함합니다.

- 최대 10개의 HDD/SSD.
- 모든 규모의 HDD/SSD 혼합.

다수의 드라이브가 여러 작업에 분산될 수 있으며, 만들 수 있는 작업 수에 대한 제한은 없습니다. 가져오기 작업의 경우 드라이브의 첫 번째 데이터 볼륨만 처리됩니다. 데이터 볼륨은 NTFS로 포맷되어 있어야 합니다.

하드 드라이브를 준비하고 WAImportExport 도구를 사용하여 데이터를 복사하는 경우, 외부 USB 어댑터를 사용할 수 있습니다. 대부분의 기성품 USB 3.0 이상 어댑터가 작동해야 합니다.

## <a name="next-steps"></a>다음 단계

* [AzCopy 명령줄 유틸리티로 데이터 전송](../storage/common/storage-use-azcopy-v10.md)
