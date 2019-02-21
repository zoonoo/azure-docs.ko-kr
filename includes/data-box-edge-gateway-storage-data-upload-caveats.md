---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/07/2019
ms.author: alkohli
ms.openlocfilehash: ad7ceffed61665a729d4391b5f0ff2935375c253
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966940"
---
다음 주의 사항은 Azure로 이동되는 데이터에 적용됩니다.

- 둘 이상의 디바이스가 같은 컨테이너에 기록하지 않도록 하는 것이 좋습니다.
- 클라우드에 복사되는 개체와 동일한 이름을 사용하는 기존 Azure 개체(예: Blob 또는 파일)가 있는 경우 디바이스는 클라우드의 파일을 덮어씁니다.
- 공유 폴더 아래에 만들어진 빈 디렉터리 계층 구조(파일 없음)는 Blob 컨테이너로 업로드되지 않습니다.
- 대용량 파일의 경우 일시적인 오류에 대한 복사 작업을 다시 시도하기 때문에 robocopy를 사용하는 것이 좋습니다.
