---
title: Azure managed disks에 대 한 공유 디스크 사용
description: 여러 Vm에서 공유할 수 있도록 공유 디스크를 사용 하 여 Azure 관리 디스크 구성 (미리 보기)
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0e925abf151abc1a229b57bb035775430fa5332f
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970373"
---
# <a name="enable-shared-disk"></a>공유 디스크 사용

이 문서에서는 Azure managed disks에 대 한 공유 디스크 (미리 보기) 기능을 사용 하도록 설정 하는 방법을 설명 합니다. Azure 공유 디스크 (미리 보기)는 여러 Vm (가상 컴퓨터)에 관리 디스크를 동시에 연결할 수 있도록 하는 Azure managed disks의 새로운 기능입니다. 관리 디스크를 여러 Vm에 연결 하면 새 클러스터 된 응용 프로그램을 배포 하거나 기존 클러스터형 응용 프로그램을 Azure로 마이그레이션할 수 있습니다. 

공유 디스크가 사용 하도록 설정 된 관리 디스크에 대 한 개념 정보를 찾고 있는 경우 [Azure 공유 디스크](disks-shared.md)를 참조 하세요.
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]