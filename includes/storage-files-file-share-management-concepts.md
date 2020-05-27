---
title: 파일 포함
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597884"
---
Azure 파일 공유는 저장소의 공유 풀을 나타내는 최상위 개체인 *저장소 계정*에 배포 됩니다. 이 저장소 풀을 사용 하 여 여러 파일 공유 뿐만 아니라 blob 컨테이너, 큐, 테이블 등의 기타 저장소 리소스를 배포할 수 있습니다. 저장소 계정에 배포 된 모든 저장소 리소스는 해당 저장소 계정에 적용 되는 제한을 공유 합니다. 저장소 계정에 대 한 현재 한도를 보려면 [Azure Files 확장성 및 성능 목표](../articles/storage/files/storage-files-scale-targets.md)를 참조 하세요.

Azure Files 배포에 사용할 저장소 계정에는 다음과 같은 두 가지 기본 유형이 있습니다. 
- **GPv2 (범용 버전 2) 저장소 계정**: GPv2 storage 계정을 사용 하면 표준/하드 디스크 기반 (HDD 기반) 하드웨어에 Azure 파일 공유를 배포할 수 있습니다. Azure 파일 공유를 저장 하는 것 외에도 GPv2 storage 계정은 blob 컨테이너, 큐 또는 테이블과 같은 다른 저장소 리소스를 저장할 수 있습니다. 
- **FileStorage storage 계정**: FileStorage storage 계정을 사용 하면 프리미엄/반도체 디스크 기반 (SSD 기반) 하드웨어에 Azure 파일 공유를 배포할 수 있습니다. FileStorage 계정은 Azure 파일 공유를 저장 하는 데만 사용할 수 있습니다. FileStorage 계정에는 다른 저장소 리소스 (blob 컨테이너, 큐, 테이블 등)를 배포할 수 없습니다.

Azure Portal, PowerShell 또는 CLI에서 제공 하는 여러 가지 다른 저장소 계정 유형이 있습니다. 블록 Blobstorage 및 BlobStorage 저장소 계정 이라는 두 저장소 계정 유형은 Azure 파일 공유를 포함할 수 없습니다. 표시 될 수 있는 다른 두 저장소 계정 유형은 GPv1 (범용 버전 1) 및 클래식 저장소 계정이 며 둘 다 Azure 파일 공유를 포함할 수 있습니다. GPv1 및 클래식 저장소 계정에는 Azure 파일 공유가 포함 될 수 있지만 Azure Files의 대부분의 새로운 기능은 GPv2 및 FileStorage 저장소 계정 에서만 사용할 수 있습니다. 따라서 새 배포에 대해 GPv2 및 FileStorage 저장소 계정만 사용 하 고 환경에 이미 있는 경우 GPv1 및 클래식 저장소 계정을 업그레이드 하는 것이 좋습니다.  