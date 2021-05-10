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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95992915"
---
- 현재 증분 스냅샷은 구독 간에 이동할 수 없습니다.
- 현재는 지정된 시간에 특정 스냅샷 패밀리 중 최대 5개 스냅샷의 SAS URI만 생성할 수 있습니다.
- 해당 디스크의 구독 외부에 있는 특정 디스크에 대해 증분 스냅샷을 만들 수 없습니다.
- 5분마다 디스크당 최대 7개의 증분 스냅샷이 만들어질 수 있습니다.
- 단일 디스크에 대해 총 200개의 증분 스냅샷을 만들 수 있습니다.
- 4TB 경계에서 부모 디스크의 크기를 변경하기 전후에 만들어진 스냅샷 간의 변경 내용을 가져올 수 없습니다. 예를 들어 디스크 크기가 2TB일 때 증분 스냅샷인 snapshot-a를 만들었습니다. 이제 디스크 크기를 6TB로 늘린 다음, 다른 증분 스냅샷인 snapshot-b를 만들었습니다. snapshot-a와 snapshot-b 간의 변경 내용을 가져올 수 없습니다. 크기 조정 후에 만든 snapshot-b의 전체 복사본을 다시 다운로드해야 합니다. 그 후에 snapshot-b와 snapshot-b 다음에 생성된 스냅샷 간의 변경 내용을 가져올 수 있습니다. 
