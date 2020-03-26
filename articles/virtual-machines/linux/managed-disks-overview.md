---
title: 디스크 스토리지 개요
description: VM을 사용할 때 스토리지 계정을 처리해주는 Azure 관리 디스크에 대한 개요
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 12/02/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: edcde9c5192791e88da57d158bab3e79b171bfc8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78944757"
---
# <a name="introduction-to-azure-managed-disks"></a>Azure Managed Disks 소개

Azure 관리 디스크는 Azure에서 관리하고 Azure Virtual Machines와 함께 사용되는 블록 수준 스토리지 볼륨입니다. 관리 디스크는 온-프레미스 서버의 물리적 디스크와 유사하지만 가상화되어 있습니다. 관리 디스크를 사용하는 경우 디스크 크기, 디스크 유형을 지정하고 디스크를 프로비저닝하기만 하면 됩니다. 디스크를 프로비저닝하면 Azure가 나머지를 처리합니다.

사용 가능한 디스크 유형은 Ultra 디스크, 프리미엄 SSD(반도체 드라이브), 표준 SSD 및 표준 HDD(하드 디스크 드라이브)입니다. 각 개별 디스크 유형에 대한 자세한 내용은 [IaaS VM의 디스크 유형 선택](disks-types.md)을 참조하세요.

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [IaaS VM의 디스크 유형 선택](disks-types.md)