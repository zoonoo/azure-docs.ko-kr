---
title: 문서 세부 사항 - Custom Translator
titleSuffix: Azure Cognitive Services
description: 문서 목록 페이지는 작업 영역에 포함된 처음 10개의 문서를 보여 줍니다. 각 문서의 이름, 페어링, 유형, 언어, 업로드 타임스탬프, 문서를 업로드한 사용자의 메일 주소가 표시됩니다.
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 267d60c2e9941c66ab128f68bd7aaf1cc54fedd9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442502"
---
# <a name="view-document-details"></a>문서 세부 정보 보기

문서 목록 페이지는 작업 영역에 포함된 처음 10개의 문서를 보여 줍니다. 각 문서의 이름, 페어링, 유형, 언어, 업로드 타임스탬프, 문서를 업로드한 사용자의 메일 주소가 표시됩니다.

개별 문서를 클릭하여 문서 세부 사항 페이지를 봅니다. 문서 세부 사항 페이지에 문서에서 추출된 문장 목록이 표시됩니다.

- 기본적으로 드롭다운 필드에서 “소스” 언어가 선택되어 있지만, 대상 언어의 문장이 표시되도록 토글할 수 있습니다.
- 기본적으로 페이지당 20개의 문장이 표시됩니다. 페이지 매김 컨트롤을 사용하여 페이지 사이를 이동할 수 있습니다.

![문서 세부 사항](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>문서 삭제

작업 영역 소유자인 사용자만 문서를 삭제할 수 있습니다. 모델에서 사용하는 문서는, 즉 훈련 프로세스 또는 배포 프로세스에 포함된 문서는 삭제할 수 없습니다.

1. 문서 페이지로 이동합니다.
2.  문서 레코드에 커서를 두고 휴지통 아이콘을 클릭합니다.

    ![문서 삭제](media/how-to/how-to-delete-document-1.png)

3.  삭제를 확인합니다.

    ![삭제 확인](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>다음 단계

- [문서를 훈련하는 방법](how-to-train-model.md)을 알아봅니다.
