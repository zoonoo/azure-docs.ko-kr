---
title: 포함 파일
description: 파일
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: f4e34e850391696506beed9f6f386f85528dff24
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284310"
---
운영 체제와 함께 미리 구성 된 Vm 중 하나를 선택한 경우 (선택적 추가 서비스) 표준 Azure VM 크기를 이미 선택 했습니다. 사전 구성된 OS로 솔루션을 시작하는 것이 좋습니다. 그러나 OS를 수동으로 설치 하는 경우 VM 이미지에서 기본 VHD의 크기를 조정 해야 합니다. OS 디스크 크기가 Linux 또는 Windows에 대 한 제한 내에 있는지 확인 하세요.

| OS | VHD 크기 |
| --- | --- |
| Linux | 30-1023 GB |
| Windows | 30-250 GB |
|

승인 된 기준으로 제공 되는 기본 Windows 또는 SQL Server 이미지는 이미 이러한 요구 사항을 충족 하므로 VHD의 형식이 나 크기를 변경 하지 마세요.

데이터 디스크는 1TB이하여야 합니다. 디스크 크기를 결정할 때 고객이 배포 시에 이미지 내에서 VHD 크기를 조정할 수 없음에 유의하세요. 데이터 디스크 Vhd를 고정 형식 Vhd로 만듭니다. 또한 확장 가능 해야 합니다 (스파스/동적). 처음에는 데이터 디스크가 비어 있거나 데이터를 포함할 수 있습니다.