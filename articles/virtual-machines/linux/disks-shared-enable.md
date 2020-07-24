---
title: Azure managed disks에 대 한 공유 디스크 사용
description: 여러 Vm에서 공유할 수 있도록 공유 디스크를 사용 하 여 Azure 관리 디스크 구성
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/16/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e36c539cc1143490aeb4862c928589db5c502656
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080229"
---
# <a name="enable-shared-disk"></a>공유 디스크 사용

이 문서에서는 Azure managed disks에 대 한 공유 디스크 기능을 사용 하도록 설정 하는 방법을 설명 합니다. Azure 공유 디스크는 여러 Vm (가상 컴퓨터)에 관리 디스크를 동시에 연결할 수 있도록 하는 Azure managed disks에 대 한 새로운 기능입니다. 관리 디스크를 여러 VM에 연결하면 새 클러스터된 애플리케이션을 배포하거나 기존 클러스터된 애플리케이션을 Azure로 마이그레이션할 수 있습니다. 

공유 디스크가 사용 하도록 설정 된 관리 디스크에 대 한 개념 정보를 찾고 있는 경우 [Azure 공유 디스크](disks-shared.md)를 참조 하세요.
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]