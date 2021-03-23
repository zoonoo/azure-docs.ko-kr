---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: lajanuar
ms.openlocfilehash: 8bf78d4c2f703ee10b26b1936620f7cf23ed7c14
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467207"
---
사용자 지정 모델 학습 데이터의 SAS URL을 검색하려면 Azure Portal의 스토리지 리소스로 이동하고 **Storage Explorer** 탭을 선택합니다. 컨테이너로 이동하고 마우스 오른쪽 단추를 클릭한 후 **공유 액세스 서명 가져오기** 를 선택합니다. 스토리지 계정 자체가 아니라 컨테이너에 대한 SAS를 가져오는 것이 중요합니다. **읽기**, **쓰기**, **삭제** 및 **목록 보기** 권한이 선택되어 있는지 확인하고 **만들기** 를 클릭합니다. 그런 다음, **URL** 섹션의 값을 임시 위치에 복사합니다. `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 형식이어야 합니다.
