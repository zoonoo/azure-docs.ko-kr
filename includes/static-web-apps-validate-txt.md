---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/10/2021
ms.author: buhollan
ms.openlocfilehash: 89e61c1cb472f7b6bb62f1f2bd84d083e103d650
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110066119"
---
#### <a name="validate-txt-record"></a>TXT 레코드 유효성 검사

1. Azure Portal의 _유효성 검사 + 구성_ 화면으로 돌아갑니다.

이 단계에서 Azure는 DNS 공급자를 통해 TXT 레코드를 자동으로 확인합니다. 유효성 검사 프로세스가 완료되면 추가된 도메인 옆에 녹색 표시가 나타납니다.

:::image type="content" source="../articles/static-web-apps/media/custom-domain/txt-record-ready.png" alt-text="TXT 레코드의 유효성 검사 완료를 나타내는 녹색 표시":::

_사용자 지정 도메인_ 화면에서 도메인 옆에 녹색 표시가 나타나면, 두 번째 단계(루트 도메인을 추가 중인 경우 ALIAS 레코드를 추가, 하위 도메인을 추가 중인 경우 CNAME 레코드를 추가)를 수행할 수 있습니다.
