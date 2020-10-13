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
ms.openlocfilehash: 8d26b6c92462abc13c42257d2c6e571156eacc0f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90563455"
---
Azure 파일 공유는 공유 스토리지 풀을 나타내는 최상위 개체인 *스토리지 계정*에 배포됩니다. 이 스토리지 풀은 여러 파일 공유는 물론 Blob 컨테이너, 큐 또는 테이블과 같은 기타 스토리지 리소스를 배포하는 데 사용할 수 있습니다. 스토리지 계정에 배포된 모든 스토리지 리소스는 해당 스토리지 계정에 적용되는 제한을 공유합니다. 스토리지 계정의 현재 제한을 보려면 [Azure Files 확장성 및 성능 대상](../articles/storage/files/storage-files-scale-targets.md)을 참조하세요.

Azure Files 배포에 사용할 스토리지 계정에는 다음과 같은 두 가지 기본 유형이 있습니다. 
- **범용 버전 2(GPv2) 스토리지 계정**: GPv2 스토리지 계정을 사용하면 표준/하드 디스크 기반(HDD 기반) 하드웨어에 Azure 파일 공유를 배포할 수 있습니다. GPv2 스토리지 계정은 Azure 파일 공유 저장 외에도 Blob 컨테이너, 큐 또는 테이블과 같은 다른 스토리지 리소스를 저장할 수 있습니다. 
- **FileStorage 스토리지 계정**: FileStorage 스토리지 계정을 사용하면 프리미엄/반도체 디스크 기반(SSD 기반) 하드웨어에 Azure 파일 공유를 배포할 수 있습니다. FileStorage 계정은 Azure 파일 공유를 저장하는 데만 사용할 수 있습니다. 다른 스토리지 리소스(Blob 컨테이너, 큐, 테이블 등)는 FileStorage 계정에 배포할 수 없습니다. FileStorage 계정만 SMB 및 NFS 파일 공유를 모두 배포할 수 있습니다.

Azure Portal, PowerShell 또는 CLI에서 제공하는 몇 가지 다른 스토리지 계정 유형이 있습니다. BlockBlobStorage 및 BlobStorage 스토리지 계정이라는 두 가지 스토리지 계정 유형은 Azure 파일 공유를 포함할 수 없습니다. 다른 두 스토리지 계정 유형은 범용 버전 1(GPv1)과 클래식 스토리지 계정이며, 두 계정 모두 Azure 파일 공유를 포함할 수 있습니다. GPv1 및 클래식 스토리지 계정에는 Azure 파일 공유가 포함될 수 있지만 Azure Files의 대부분의 새 기능은 GPv2 및 FileStorage 스토리지 계정에서만 사용할 수 있습니다. 따라서 새 배포에 대해 GPv2 및 FileStorage 스토리지 계정만 사용하고 환경에 이미 있는 경우 GPv1 및 클래식 스토리지 계정을 업그레이드하는 것이 좋습니다.  