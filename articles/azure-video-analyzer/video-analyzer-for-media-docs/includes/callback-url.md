---
author: Juliako
ms.topic: include
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: db2f6fe6175b8ff4d6371cb7074fb8c83af51c14
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386906"
---
POST 요청을 사용하여 고객에게 다음 이벤트를 알리는 데 사용되는 URL입니다.

- 인덱싱 상태 변경 
    - 속성:    
    
        |Name|Description|
        |---|---|
        |id|비디오 ID|
        |state|비디오 상태|  
    - 예: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- 비디오에서 식별된 사용자
  - 속성
    
      |이름|Description|
      |---|---|
      |id| 비디오 ID|
      |faceId|비디오 인덱스에 표시되는 얼굴 ID|
      |knownPersonId|얼굴 모델 내에서 고유한 사람 ID|
      |personName|사람의 이름|
        
    - 예: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 
