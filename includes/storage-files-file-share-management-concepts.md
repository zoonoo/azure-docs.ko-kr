---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597884"
---
Azure 파일 공유는 *저장소의*공유 풀을 나타내는 최상위 개체인 저장소 계정에 배포됩니다. 이 저장소 풀은 여러 파일 공유뿐만 아니라 Blob 컨테이너, 큐 또는 테이블과 같은 다른 저장소 리소스를 배포하는 데 사용할 수 있습니다. 저장소 계정에 배포되는 모든 저장소 리소스는 해당 저장소 계정에 적용되는 제한을 공유합니다. 저장소 계정에 대한 현재 제한을 보려면 [Azure Files 확장성 및 성능 목표를](../articles/storage/files/storage-files-scale-targets.md)참조하십시오.

Azure Files 배포에 사용할 두 가지 주요 저장소 계정 유형이 있습니다. 
- **범용 버전 2(GPv2) 저장소 계정**: GPv2 저장소 계정을 사용하면 표준/하드 디스크 기반(HDD 기반) 하드웨어에 Azure 파일 공유를 배포할 수 있습니다. GPv2 저장소 계정은 Azure 파일 공유를 저장하는 것 외에도 Blob 컨테이너, 큐 또는 테이블과 같은 다른 저장소 리소스를 저장할 수 있습니다. 
- **FileStorage 저장소 계정**: FileStorage 저장소 계정을 사용하면 프리미엄/솔리드 스테이트 디스크 기반(SSD 기반) 하드웨어에 Azure 파일 공유를 배포할 수 있습니다. FileStorage 계정은 Azure 파일 공유를 저장하는 데만 사용할 수 있습니다. 다른 저장소 리소스(Blob 컨테이너, 큐, 테이블 등)는 FileStorage 계정에 배포할 수 없습니다.

Azure 포털, PowerShell 또는 CLI에서 사용할 수 있는 다른 여러 저장소 계정 유형이 있습니다. 두 개의 저장소 계정 유형인 BlockBlobStorage 및 BlobStorage 저장소 저장소 계정은 Azure 파일 공유를 포함할 수 없습니다. 표시되는 다른 두 저장소 계정 유형은 범용 버전 1(GPv1) 및 클래식 저장소 계정으로, 둘 다 Azure 파일 공유를 포함할 수 있습니다. GPv1 및 클래식 저장소 계정에Azure 파일 공유가 포함될 수 있지만 Azure 파일의 대부분의 새 기능은 GPv2 및 File Storage 저장소 계정에서만 사용할 수 있습니다. 따라서 새 배포에 대해 GPv2 및 FileStorage 저장소 계정만 사용하고 환경에 이미 있는 경우 GPv1 및 클래식 저장소 계정을 업그레이드하는 것이 좋습니다.  