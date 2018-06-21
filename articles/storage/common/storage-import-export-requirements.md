---
title: Azure Import/Export 서비스에 대한 요구 사항 | Microsoft Docs
description: Azure Import/Export 서비스에 대한 소프트웨어 및 하드웨어 요구 사항을 이해합니다.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/06/2018
ms.author: alkohli
ms.openlocfilehash: 4c6e22f50f4550cb4a6e25960bcc13a4d92e9819
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825070"
---
# <a name="azure-importexport-system-requirements"></a>Azure Import/Export에 대한 시스템 요구 사항

이 문서에서는 Azure Import/Export 서비스에 대한 중요한 요구 사항을 설명합니다. Import/Export 서비스를 사용하기 전에 정보를 신중하게 검토한 다음, 작업 중 필요에 따라 다시 검토하는 것이 좋습니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

WAImportExport 도구를 사용하여 하드 드라이브를 준비하기 위해 다음과 같은 **BitLocker 드라이브 암호화를 지원하는 64비트 OS**가 지원됩니다.


|플랫폼 |버전 |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> 윈도우 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |



## <a name="supported-storage-accounts"></a>지원되는 저장소 계정

Azure Import/Export 서비스는 다음과 같은 Azure 저장소 계정을 지원합니다.
- 클래식
- Blob Storage 계정
- 범용 v1 저장소 계정입니다. 

각 작업은 하나의 저장소 계정에서만 데이터 전송에 사용될 수 있습니다. 다시 말해, 하나의 가져오기/내보내기 작업이 여러 저장소 계정에서 사용될 수 없습니다. 새 Storage 계정 만들기에 대한 자세한 내용은 [Storage 계정을 만드는 방법](storage-create-storage-account.md#create-a-storage-account)(영문)을 참조하세요.

> [!IMPORTANT] 
> Azure 가져오기 내보내기 서비스는 [Virtual Network 서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md) 기능이 활성화된 저장소 계정을 지원하지 않습니다. 

## <a name="supported-storage-types"></a>지원되는 저장소 형식

다음 저장소 유형 목록은 Azure Import/Export 서비스를 지원합니다.


|작업  |Storage  |지원됨  |지원되지 않음  |
|---------|---------|---------|---------|
|가져오기     |  Azure Blob 저장소. <br>블록 Blob, 페이지 Blob 지원됨. <br> Azure Files 지원됨.       |         |
|내보내기     |   Azure Blob 저장소. <br>블록 Blob, 페이지 Blob 및 추가 Blob 지원됨.       | Azure Files 지원되지 않음.        |


## <a name="supported-hardware"></a>지원되는 하드웨어 

Azure Import/Export 서비스의 경우 데이터를 복사할 지원되는 디스크 및 지원되는 SATA 커넥터가 필요합니다.

### <a name="supported-disks"></a>지원되는 디스크

다음 디스크 목록은 Import/Export 서비스에서 사용하도록 지원됩니다.


|디스크 유형  |크기  |지원됨 |지원되지 않음  |
|---------|---------|---------|---------|
|SSD    |   2.5"      |         |         |
|HDD     |  2.5"<br>3.5"       |SATA II, SATA III         |외부 HDD(기본 제공 USB 어댑터 포함) <br> 디스크 내 외부 HDD 케이싱         |


단일 가져오기/내보내기 작업은 다음을 포함합니다.
- 최대 10개의 HDD/SSD.
- 모든 규모의 HDD/SSD 혼합.

다수의 드라이브가 여러 작업에 분산될 수 있으며, 만들 수 있는 작업 수에 대한 제한은 없습니다. 

가져오기 작업의 경우 드라이브의 첫 번째 데이터 볼륨만 처리됩니다. 데이터 볼륨은 NTFS로 포맷되어 있어야 합니다.

### <a name="supported-external-usb-adaptors"></a>지원되는 외부 USB 어댑터

하드 드라이브를 준비하고 WAImportExport 도구를 사용하여 데이터를 복사하는 경우, 다음 외부 USB 어댑터(기성품)를 사용할 수 있습니다. 
- Anker 68UPSATAA-02BU
- Anker 68UPSHHDS-BU
- Startech SATADOCK22UE
- Orico 6628SUS3-C-BK(6628 시리즈)
- Thermaltake BlacX Hot-Swap SATA 외부 하드 드라이브 도킹 스테이션(USB 2.0 및 eSATA)


## <a name="next-steps"></a>다음 단계

* [WAImportExport 도구 설정](storage-import-export-tool-how-to.md)
* [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)
* [Azure Import/Export REST API 샘플](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

