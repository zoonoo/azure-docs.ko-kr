---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67182840"
---
비행 중인 데이터의 경우:

- 표준 TLS 1.2는 장치와 Azure 간에 이동하는 데이터에 사용됩니다. TLS 1.1 이전으로의 대체는 없습니다. TLS 1.2가 지원되지 않으면 에이전트 통신이 차단됩니다. 포털 및 SDK 관리를 위해서는 TLS 1.2도 필요합니다.
- 클라이언트가 브라우저의 로컬 웹 UI를 통해 장치에 액세스하는 경우 표준 TLS 1.2가 기본 보안 프로토콜로 사용됩니다.

    - 가장 좋은 방법은 TLS 1.2를 사용하도록 브라우저를 구성하는 것입니다.
    - 브라우저에서 TLS 1.2를 지원하지 않는 경우 TLS 1.1 또는 TLS 1.0을 사용할 수 있습니다.
- SMB 3.0을 암호화와 함께 사용하여 데이터 서버에서 데이터를 복사할 때 데이터를 보호하는 것이 좋습니다.
