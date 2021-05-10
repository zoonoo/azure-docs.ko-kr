---
author: craigshoemaker
ms.service: static-web-apps
ms.topic: include
ms.date: 03/25/2021
ms.author: cshoe
ms.openlocfilehash: 806a30214e0307b8fa101c0de51ed2f16622d433
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543563"
---
| 속성 | 설명 | 예 | 필수 |
|---|---|---|---|
| `app_location` | 애플리케이션 코드의 위치입니다. | 애플리케이션 소스 코드가 리포지토리의 루트에 있는 경우 `/`를 입력하고, 애플리케이션 코드가 `app`이라는 디렉터리에 있는 경우 `/app`을 입력합니다. | 예 |
| `api_location` | Azure Functions 코드의 위치입니다. | 앱 코드가 `api`라는 폴더에 있는 경우 `/api`를 입력합니다. 폴더에서 Azure Functions 앱이 검색되지 않는 경우 빌드가 실패하지 않으며 워크플로는 사용자가 API를 원하지 않는다고 가정합니다. | 예 |
| `output_location` | `app_location`에 상대적인 빌드 출력 디렉터리의 위치입니다. | 애플리케이션 소스 코드가 `/app`에 있고, 빌드 스크립트에서 `/app/build` 폴더에 파일을 출력하는 경우 `build`를 `output_location` 값으로 설정합니다. | 예 |