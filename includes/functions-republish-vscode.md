---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/20/2020
ms.author: glenga
ms.openlocfilehash: 574756aa9d9bac4aa42febf6a4fca4c62014db3f
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84732477"
---
1. Visual Studio Code에서 F1 키를 선택하여 명령 팔레트를 엽니다. 명령 팔레트에서 **Azure Functions: 함수 앱에 배포**를 검색하여 선택합니다.

1. 로그인하지 않은 경우 **Azure에 로그인**하라는 메시지가 표시됩니다. 브라우저에서 로그인한 후 Visual Studio Code로 돌아갑니다. 구독이 여러 개 있는 경우 함수 앱이 포함된 **구독을 선택**합니다.

1. Azure에서 기존 함수 앱을 선택합니다. 함수 앱의 모든 파일을 덮어쓰는 방법에 대한 경고가 표시되면 **배포**를 선택하여 경고를 확인하고 계속합니다.

프로젝트를 다시 빌드하고, 다시 패키지하여 Azure에 업로드합니다. 기존 프로젝트가 새 패키지로 바뀌고 함수 앱이 다시 시작됩니다.