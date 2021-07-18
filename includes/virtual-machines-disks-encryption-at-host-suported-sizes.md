---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e9d7b39ce267202503b90e84e934d31501d45478
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732339"
---
모든 최신 세대 VM 크기는 호스트에서 암호화를 지원합니다.

|형식  |지원되지 않음  |지원됨  |
|---------|---------|---------|
|범용     | Dv3, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dav4, Dasv4, Ddv4, Ddsv4       |
|컴퓨팅 최적화     |         | Fsv2        |
|메모리 최적화     | Ev3        | Esv3, M, Mv2, Eav4, Easv4, Edv4, Edsv4        |
|스토리지 최적화     |         | Ls, Lsv2(NVMe 디스크는 암호화되지 않음)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2(미리 보기)        |
|고성능 컴퓨팅     | H        | HB, HC, HBv2        |
|이전 세대     | F, A, D, L, G        | DS, GS, Fs, NVv2        |
