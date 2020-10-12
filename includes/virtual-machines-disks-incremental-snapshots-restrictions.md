---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4af8a150b062526f08c1d15581ec26e2fe12d8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87102831"
---
- 현재 증분 스냅숏은 구독 간에 이동할 수 없습니다.
- 현재는 특정 시간에 특정 스냅숏 패밀리의 최대 5 개 스냅숏의 SAS Uri만 생성할 수 있습니다.
- 해당 디스크의 구독 외부에 있는 특정 디스크에 대해 증분 스냅숏을 만들 수는 없습니다.
- 디스크당 최대 7 개의 증분 스냅숏이 5 분 마다 만들어질 수 있습니다.
- 단일 디스크에 대해 총 200 증분 스냅숏을 만들 수 있습니다.
- 4 TB 경계에서 부모 디스크의 크기를 변경 하기 전과 후에 수행 된 스냅숏 간의 변경 내용을 가져올 수 없습니다. 예를 들어 디스크 크기가 2tb 인 경우 증분 스냅숏 스냅숏 (a)을 수행 했습니다. 이제 디스크 크기를 6TB로 늘린 다음 다른 증분 스냅숏 스냅숏-b를 수행 했습니다. 스냅숏 a와 스냅숏-b 간의 변경 내용을 가져올 수 없습니다. 크기 조정 후에 만든 snapshot-b의 전체 복사본을 다시 다운로드 해야 합니다. 그런 다음 스냅숏-b와 스냅숏-b 이후에 생성 된 스냅숏 간의 변경 내용을 가져올 수 있습니다. 
