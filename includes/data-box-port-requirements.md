---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67839757"
---
| 포트 번호| 인 또는 아웃 | 포트 범위| 필수| 메모 |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80(HTTP)|그런 다음|LAN|yes|이 포트는 HTTP를 통해 데이터 상자 블로그 저장소 REST API에 연결하는 데 사용됩니다. REST API에 연결하지 않으면 8443을 통해 로컬 웹 UI로 자동으로 리디렉션됩니다. |
| TCP 443(HTTPS)|그런 다음|LAN|yes|이 포트는 HTTPS를 통해 데이터 상자 블로그 저장소 REST API에 연결하는 데 사용됩니다. REST API에 연결하지 않으면 8443을 통해 로컬 웹 UI로 자동으로 리디렉션됩니다. |
| TCP 8443 (HTTPS-Alt)|그런 다음|LAN|yes|이 포트는 HTTPS의 대체 포트이며 장치 관리를 위해 로컬 웹 UI에 연결할 때 사용됩니다. |
| TCP 445(SMB)|아웃/인|LAN|일부 경우<br>참고 사항 보기|이 포트는 SMB를 통해 연결하는 경우에만 필요합니다. |
| TCP 2049(NFS)|아웃/인|LAN|일부 경우<br>참고 사항 보기|이 포트는 NFS를 통해 연결하는 경우에만 필요합니다. |
| TCP 111 (NFS)|아웃/인|LAN|일부 경우<br>참고 사항 보기|이 포트는 rpcbind/포트 매핑에 사용되며 NFS를 통해 연결하는 경우에만 필요합니다.  |
