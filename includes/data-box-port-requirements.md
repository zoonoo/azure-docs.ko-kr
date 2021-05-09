---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/06/2020
ms.author: alkohli
ms.openlocfilehash: b9ff5968b4bb406f1a96780985b5c6fe64ca976c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "89505911"
---
| 포트 번호| 인 또는 아웃 | 포트 범위| 필수| 메모 |
|--------|-----|-----|-----------|----------|
| TCP 80(HTTP)|In(다음 안에)|LAN|예|이 포트는 HTTP를 통해 Data Box Blob 스토리지 REST API에 연결하는 데 사용됩니다. REST API에 연결하지 않는 경우 8443을 통해 로컬 웹 UI로 자동 리디렉션됩니다. |
| TCP 443(HTTPS)|In(다음 안에)|LAN|예|이 포트는 HTTPS를 통해 Data Box Blob 스토리지 REST API에 연결하는 데 사용됩니다. REST API에 연결하지 않는 경우 8443을 통해 로컬 웹 UI로 자동 리디렉션됩니다. |
| TCP 8443(HTTPS-Alt)|In(다음 안에)|LAN|예|이는 HTTPS의 대체 포트이며, 디바이스 관리를 위해 로컬 웹 UI에 연결할 때 사용됩니다. |
| TCP 445(SMB)|아웃/인|LAN|일부 경우<br>참고 사항 보기|이 포트는 SMB를 통해 연결하는 경우에만 필요합니다. |
| TCP 2049(NFS)|아웃/인|LAN|일부 경우<br>참고 사항 보기|이 포트는 NFS를 통해 연결하는 경우에만 필요합니다. |
| TCP 111(NFS)|아웃/인|LAN|일부 경우<br>참고 사항 보기|이 포트는 rpcbind/port 매핑에 사용되며 NFS를 통해 연결하는 경우에만 필요합니다.  |
