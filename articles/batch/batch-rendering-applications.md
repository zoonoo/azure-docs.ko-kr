---
title: Batch 렌더링 응용 프로그램
description: 미리 설치된 Batch 렌더링 응용 프로그램
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 28acd1b7275694d38a52f14d2b2c32b79cc8183e
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2018
ms.locfileid: "40036809"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>렌더링 VM 이미지에 미리 설치된 응용 프로그램

Azure Batch를 모든 렌더링 응용 프로그램에 사용할 수 있습니다. 그러나 Azure Marketplace VM 이미지는 미리 설치된 일반적인 응용 프로그램에 사용할 수 있습니다.

해당하는 경우 미리 설치된 렌더링 응용 프로그램에 종량제 라이선스를 사용할 수 있습니다. Batch 풀을 만들 때 필요한 응용 프로그램을 지정할 수 있고 VM 및 응용 프로그램 비용이 분 단위로 청구됩니다. 응용 프로그램 가격은 [Azure Batch 가격 책정 페이지](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)에 나열되어 있습니다.

일부 응용 프로그램은 Windows만 지원하나 대부분 Windows와 Linux를 모두 지원합니다.

## <a name="applications-on-centos-7-rendering-nodes"></a>CentOS 7의 응용 프로그램 렌더링 노드

* Autodesk Maya I/O 2017 업데이트 5(201708032230 잘라내기)
* Autodesk Maya I/O 2018 업데이트 2(201711281015 컷)
* Autodesk Arnold for Maya 2017(Arnold 버전 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya 2018(Arnold 버전 5.0.1.4) MtoA-2.1.0.3-2018
* Maya 2017용 Chaos Group V-Ray(버전 3.60.04)
* Maya 2018용 Chaos Group V-Ray(버전 3.60.04)
* Blender(2.68)

## <a name="applications-on-windows-server-2016-rendering-nodes"></a>Windows Server 2016의 응용 프로그램 렌더링 노드

* Autodesk Maya I/O 2017 업데이트 5(버전 17.4.5459)
* Autodesk Maya I/O 2018 업데이트 3(버전 18.3.0.7040)  
* Autodesk 3ds Max I/O 2019 업데이트 1(버전 21.10.1314)
* Autodesk 3ds Max I/O 2018 업데이트 4(버전 20.4.0.4254)
* Maya용 Autodesk Arnold(Arnold 버전 5.0.1.1) MtoA-2.0.1.1-2017
* Maya용 Autodesk Arnold(Arnold 버전 5.0.1.4) MtoA-2.0.2.3-2018
* 3ds Max용 Autodesk Arnold(Arnold 버전 5.0.2.4) (버전 1.2.926)
* Maya용 Chaos Group V-Ray(버전 3.52.03)
* 3ds Max용 Chaos Group V-Ray(버전 3.60.02)
* Blender(2.79)

## <a name="next-steps"></a>다음 단계

렌더링 VM 이미지를 사용하려면 풀을 만들 때 풀 구성에서 지정해야 합니다. [렌더링을 위한 Batch 풀 기능](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools)을 참조하세요.