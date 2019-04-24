---
title: Microsoft Azure Data Box Disk 시스템 요구 사항 | Microsoft Docs
description: Azure Data Box Disk의 소프트웨어 및 네트워킹 요구 사항에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/22/2019
ms.author: alkohli
ms.openlocfilehash: 067bb6b806ddd9b83d4ea755876a980ca45d76e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387471"
---
# <a name="azure-data-box-disk-system-requirements"></a>Azure Data Box Disk 시스템 요구 사항

이 문서에서는 Microsoft Azure Data Box Disk 솔루션 및 Data Box Disk에 연결하는 클라이언트에 대한 중요한 시스템 요구 사항을 설명합니다. Data Box Disk를 배포하기 전에 정보를 신중하게 검토하고 배포 및 후속 작업 중 필요에 따라 다시 검토하는 것이 좋습니다.

시스템 요구 사항은 디스크에 연결하는 클라이언트에 대해 지원되는 플랫폼, 지원되는 저장소 계정 및 저장소 형식을 포함합니다.


## <a name="supported-operating-systems-for-clients"></a>클라이언트에 대해 지원되는 운영 체제

Data Box Disk에 연결된 클라이언트를 통해 디스크 잠금 해제 및 데이터 복사 작업에 대해 지원되는 운영 체제의 목록은 다음과 같습니다.

| **운영 체제** | **테스트 버전** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> RHEL(Red Hat Enterprise Linux) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Windows 클라이언트에 필요한 다른 소프트웨어

Windows 클라이언트의 경우 다음 항목도 설치해야 합니다.

| **소프트웨어**| **버전** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Linux 클라이언트에 필요한 다른 소프트웨어

Linux 클라이언트의 경우 Data Box Disk 도구 집합이 다음 필수 소프트웨어를 설치합니다.

- dislocker
- OpenSSL

## <a name="supported-connection"></a>지원되는 연결

데이터를 포함하는 클라이언트 컴퓨터에 USB 3.0 이상 포트가 있어야 합니다. 제공된 케이블을 사용하여 디스크를 이 클라이언트에 연결합니다.

## <a name="supported-storage-accounts"></a>지원되는 저장소 계정

Data Box Disk에 대해 지원되는 저장소 형식의 목록은 다음과 같습니다.

| **저장소 계정** | **참고 사항** |
| --- | --- |
| 클래식 | Standard |
| 범용  |표준; V1 및 V2 모두 지원됩니다. 핫 및 쿨 계층은 모두 지원됩니다. |
| Blob Storage 계정 | |

>[!NOTE]
> Azure Data Lake Storage Gen 2 계정은 지원되지 않습니다.


## <a name="supported-storage-types-for-upload"></a>업로드에 대 한 지원 되는 저장소 유형

Data Box 디스크를 사용 하 여 Azure에 업로드에 대 한 지원 저장소 형식의 목록은 다음과 같습니다.

| **파일 형식** | **참고 사항** |
| --- | --- |
| Azure 블록 Blob | |
| Azure 페이지 Blob  | |
| Azure 파일  | |
| Managed Disks | |


## <a name="next-step"></a>다음 단계

* [Azure Data Box Disk 배포](data-box-disk-deploy-ordered.md)

