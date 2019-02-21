---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3d100ec08b7b6d70366e605daf9c67edc6ab3bf3
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331071"
---
Premium Storage 계정에는 다음과 같은 확장성 목표가 있습니다.

| 총 계정 용량 | 로컬 중복 저장소 계정의 총 대역폭 |
| --- | --- | 
| 디스크 용량: 35TB <br>스냅숏 용량: 10TB | 인바운드<sup>1</sup> + 아웃바운드<sup>2</sup>에 대해 초당 최대 50기가비트 |

<sup>1</sup> 저장소 계정으로 전송되는 모든 데이터(요청)

<sup>2</sup> 저장소 계정에서 수신하는 모든 데이터(응답)

관리되지 않는 디스크에 Premium Storage 계정을 사용하는 경우 응용 프로그램이 단일 스토리지 계정의 확장성 목표를 초과하면 관리 디스크로 마이그레이션하려고 할 수 있습니다. 관리 디스크로 마이그레이션하지 않으려면 애플리케이션이 다수의 저장소 계정을 사용하도록 빌드합니다. 그런 다음 이 저장소 계정 간에 데이터를 분할합니다. 예를 들어 51TB 디스크를 여러 VM에 연결하려는 경우에는 두 개의 저장소 계정에 분산합니다. 단일 Premium Storage 계정의 한도는 35TB입니다. 단일 Premium Storage 계정에 35TB를 넘는 프로비전된 디스크가 없도록 해야 합니다.