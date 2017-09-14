---
title: "Azure Container Instances 지역 및 리소스 가용성 | Azure Docs"
description: "컨테이너 인스턴스의 배포 및 해당 인스턴스에 대한 CPU 및 메모리 제한을 지원하는 Azure 지역을 검색합니다."
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: marsma
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 3eb68cba15e89c455d7d33be1ec0bf596df5f3b7
ms.openlocfilehash: 2b9b1b864bbfd73383759212dd7d91f8e4941544
ms.contentlocale: ko-kr
ms.lasthandoff: 09/01/2017

---

# <a name="region-availability-for-azure-container-instances"></a>Azure Container Instances에 대한 지역 가용성

미리 보기 중 Azure Container Instances를 지정된 CPU 및 메모리 제한을 사용하여 다음 지역에서 사용할 수 있습니다.

| 위치 | OS | CPU | 메모리(GB) |
| -------- | -- | :---: | :-----------: |
| 유럽 서부, 미국 서부, 미국 동부 | Linux | 2 | 7 |
| 유럽 서부, 미국 서부, 미국 동부 | Windows | 2 | 3.5 |

## <a name="resource-availability"></a>리소스 가용성

이러한 리소스 제한 내에서 만든 컨테이너 인스턴스는 배포 지역 내의 사용 가능 여부의 적용을 받습니다. 영역이 과부하 상태에 있는 경우 인스턴스를 배포할 때 오류가 발생할 수 있습니다.

이러한 배포 오류를 완화하려면 낮은 CPU 및 메모리 설정을 사용하여 인스턴스를 배포하거나 나중에 배포를 시도합니다.

## <a name="next-steps"></a>다음 단계

컨테이너 인스턴스 배포 문제 해결에 대한 자세한 내용은 [Azure Container Instances로의 배포 문제 해결](container-instances-troubleshooting.md)을 참조하세요.
