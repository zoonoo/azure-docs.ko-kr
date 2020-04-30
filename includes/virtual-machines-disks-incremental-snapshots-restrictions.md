---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 39a013f5f4b587137366147ade77f0be1b353c4c
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204520"
---
- 현재 증분 스냅숏은 구독 간에 이동할 수 없습니다.
- 현재는 특정 시간에 특정 스냅숏 패밀리의 최대 5 개 스냅숏의 SAS Uri만 생성할 수 있습니다.
- 해당 디스크의 구독 외부에 있는 특정 디스크에 대해 증분 스냅숏을 만들 수는 없습니다.
- 디스크당 최대 7 개의 증분 스냅숏이 5 분 마다 만들어질 수 있습니다.
- 단일 디스크에 대해 총 200 증분 스냅숏을 만들 수 있습니다.
- 4 TB 경계에서 부모 디스크의 크기 변경 전후에 수행 된 스냅숏 간의 변경 내용을 가져올 수 없습니다. 크기 조정 후 만든 스냅숏의 전체 복사본을 다시 다운로드 해야 합니다. 그런 다음 4 TB 경계를 넘어 크기를 조정한 후 생성 된 스냅숏 간의 변경 내용을 가져올 수 있습니다. 
