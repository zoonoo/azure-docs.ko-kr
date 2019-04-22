---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: bea14544949f90290bf3878295b49f1e08be9eea
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684436"
---
데이터-에-항공권:

- 장치와 Azure 간에 이동 하는 데이터에 대 한 표준 TLS 1.2 사용 됩니다. TLS 1.1 및 이전 버전에 없는 대체 방법이 있습니다. TLS 1.2를 지원 하지 않는 경우 에이전트 통신이 차단 됩니다. TLS 1.2 포털 및 SDK 관리 작업에도 필요 합니다.
- 클라이언트 장치의 로컬 웹 UI 통해 브라우저에서에 액세스 하는 경우 표준 TLS 1.2는 기본 보안 프로토콜로 사용 됩니다.

    - TLS 1.2를 사용 하도록 브라우저를 구성 하는 것이 좋습니다.
    - 브라우저는 TLS 1.2를 지원 하지 않으면, TLS 1.1 또는 TLS 1.0을 사용할 수 있습니다.
- 데이터 서버 로부터 복사할 때 데이터를 보호 하려면 암호화를 사용 하 여 SMB 3.0을 사용 하는 것이 좋습니다.
