---
title: Microsoft Azure Data Box Disk 시스템 요구 사항 | Microsoft Docs
description: Azure Data Box Disk의 소프트웨어 및 네트워킹 요구 사항에 대해 알아봅니다.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2018
ms.author: alkohli
ms.openlocfilehash: aaa4e4bb24ca42adb9d283e6286dbef879bcb1ea
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299851"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Azure Data Box Disk 시스템 요구 사항(미리 보기)

이 문서에서는 Microsoft Azure Data Box Disk 솔루션 및 Data Box Disk에 연결하는 클라이언트에 대한 중요한 시스템 요구 사항을 설명합니다. Data Box Disk를 배포하기 전에 정보를 신중하게 검토하고 배포 및 후속 작업 중 필요에 따라 다시 검토하는 것이 좋습니다.

> [!IMPORTANT]
> Data Box Disk는 미리 보기로 제공됩니다. 이 솔루션을 배포하기 전에 [미리 보기에 대한 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 검토하세요. 

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

## <a name="supported-storage-accounts"></a>지원되는 저장소 계정

Data Box Disk에 대해 지원되는 저장소 형식의 목록은 다음과 같습니다.

| **Storage 계정** | **참고 사항** |
| --- | --- |
| 클래식 | Standard |
| 범용  |표준; V1 및 V2 모두 지원됩니다. 핫 및 쿨 계층은 모두 지원됩니다. |


## <a name="supported-storage-types"></a>지원되는 저장소 형식

Data Box Disk에 대해 지원되는 저장소 형식의 목록은 다음과 같습니다.

| **파일 형식** | **참고 사항** |
| --- | --- |
| Azure 블록 Blob | |
| Azure 페이지 Blob  | |


## <a name="next-step"></a>다음 단계

* [Azure Data Box Disk 배포](data-box-disk-deploy-ordered.md)

