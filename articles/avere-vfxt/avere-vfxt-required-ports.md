---
title: Avere vFXT for Azure용 포트 허용 목록
description: Avere vFXT for Azure에서 사용하는 포트에 대한 목록을 제공합니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 394415ffc7593d058d8d0bf1c0040b88ec25073b
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669930"
---
# <a name="required-ports"></a>필수 포트

이 섹션에 나열된 포트는 vFXT 인바운드 및 아웃바운드 통신에 사용됩니다.

vFXT 클러스터 또는 클러스터 컨트롤러 인스턴스를 공용 인터넷에 직접 공개하지 마세요.

## <a name="api"></a>API

| 인바운드: | | |
| --- | ---- | --- |
| TCP | 22  | SSH  |
| TCP | 443 | HTTPS|



| 아웃바운드: |     |       |
|----------|-----|-------|
| TCP      | 443 | HTTPS |

 
## <a name="nfs"></a>NFS

| 인바운드 및 아웃바운드:  | | |
| --- | --- | ---|
| TCP/UDP | 111  | RPCBIND  |
| TCP/UDP | 2049 | NFS      |
| TCP/UDP | 4045 | NLOCKMGR |
| TCP/UDP | 4046 | MOUNTD   |
| TCP/UDP | 4047 | 상태   |

