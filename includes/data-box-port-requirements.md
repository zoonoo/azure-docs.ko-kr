---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/06/2020
ms.author: alkohli
ms.openlocfilehash: b9ff5968b4bb406f1a96780985b5c6fe64ca976c
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505911"
---
| 포트 번호| 인 또는 아웃 | 포트 범위| 필수| 참고 |
|--------|-----|-----|-----------|----------|
| TCP 80(HTTP)|In(다음 안에)|LAN|예|이 포트는 HTTP를 통해 Data Box Blob storage REST Api에 연결 하는 데 사용 됩니다. REST Api에 연결 하지 않으면 8443를 통해 로컬 웹 UI로 자동 리디렉션됩니다. |
| TCP 443(HTTPS)|In(다음 안에)|LAN|예|이 포트는 HTTPS를 통해 Data Box Blob storage REST Api에 연결 하는 데 사용 됩니다. REST Api에 연결 하지 않으면 8443를 통해 로컬 웹 UI로 자동 리디렉션됩니다. |
| TCP 8443 (HTTPS-Alt)|In(다음 안에)|LAN|예|이는 HTTPS의 대체 포트 이며, 장치 관리를 위해 로컬 웹 UI에 연결할 때 사용 됩니다. |
| TCP 445(SMB)|아웃/인|LAN|일부 경우<br>참고 사항 보기|이 포트는 SMB를 통해 연결 하는 경우에만 필요 합니다. |
| TCP 2049(NFS)|아웃/인|LAN|일부 경우<br>참고 사항 보기|이 포트는 NFS를 통해 연결 하는 경우에만 필요 합니다. |
| TCP 111 (NFS)|아웃/인|LAN|일부 경우<br>참고 사항 보기|이 포트는 rpcbind/포트 매핑에 사용 되며 NFS를 통해 연결 하는 경우에만 필요 합니다.  |
