---
title: Azure Sentinel 미리 보기에서 notebook을 사용 하 여 찾거나 기능 | Microsoft Docs
description: 이 문서에서는 Azure Sentinel hunting 기능을 사용 하 여 notebook을 사용 하는 방법을 설명 합니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 63ce2be847017ed7e80fe5e573d5553311f6af2f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107681"
---
# <a name="use-notebooks-to-hunt-for-anomalies"></a>Notebook을 사용 하 여 문제에 대 한 사냥 하

> [!IMPORTANT]
> Azure Sentinel 현재 공개 미리 보기 중입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Sentinel 활용 Jupyter 대화형 전자 필기장을 조사 하거나 사용자 환경 내에서 보안 문제에 대 한 hunt 작업과 대 한 정보를 제공 합니다. Microsoft의 보안 분석가으로 개발 된 notebook을 사용 하 여 미리 로드 된 azure Sentinel 제공 됩니다. 각 노트북은 특정 사용 사례에 대 한 자체 포함 된 워크플로 사용 하 여 작성 하는 용도입니다. 시각화는 더 빠른 데이터 탐색 및 찾거나 위협에 대 한 각 노트북에 포함 됩니다. 요구 사항에 맞게 새 노트북부터에서 만들거나 Azure Sentinel에서 notebook을 가져오는 기본 제공 노트북을 사용자 지정 ' GitHub 커뮤니티입니다. Jupyter notebook은 Azure Notebooks 페이지에서 프로젝트를 가져올 –이 통해 모든 Azure Sentinel 전자 필기장 한곳에서 액세스 하는 사용자입니다. Notebook의 단추 클릭을 사용 하 여 실행할 수 있습니다 하거나 해당 환경과 일치 하는 사용자가 구성할 수 있습니다.

완전히 통합 된 환경을 기본 유지 관리 오버 헤드 없이 클라우드 계산 및 저장소에서 실행 하려면 노트북을 수 있습니다. 기존 Jupyter Notebook 에코 시스템을 활용 하는 기능을 사용 하면 고객 데이터를 공유 않고 크 라우드 원본 모델에 할 수 있습니다. 


각 노트북을 Azure Notebooks (현재 미리 보기), Azure 클라우드에서 대화형 개발 환경에서 호스트 됩니다. Notebook은 항상 액세스할 수 있는, 세계 어디에서 나 모든 브라우저에서 항상 사용할 수 있습니다.  Azure Sentinel' 조사 및 찾거나 기본 제공 전자 필기장에 속하는 및 수정 하 고 환경에 맞게 조정할 수 있는 프로젝트에 복제 됩니다. 가장 인기 있는 기본 제공 노트북은 다음과 같습니다.

- **경고 조사 및 hunting**: 이 대화형 전자 필기장 관련된 활동 및 경고가 생성 된 데이터를 사용 하 여 경고를 정제 하 고 관련된 활동을 검색 하 여 경고의 서로 다른 클래스의 신속한 심사를 수 있습니다.

- **끝점 호스트 기반 hunting**: 끝점 호스트를 사용 하 여 연결 된 보안 관련 활동을 드릴 다운 하 여 보안 위반의 증상에 대 한 hunt 수 있습니다.  

- **Office 로그인 렵 단계별**: Office 365 데이터에서 파생 된 비정상적인 로그인 패턴을 표시할 수 있을 뿐만 아니라 주의 대상 로그의 지리적 위치를 시각화 하 여 의심 스러운 로그인에 대 한 hunt 할 수 있습니다.

## <a name="run-a-notebook"></a>Notebook 실행
다음 예의 경우 예기치 않은 위치에 있는 모든 사용자가 수행 하는 네트워크에서 어떤 일을 확인 하려면 비정상 위치를 심층 살펴보기 검색 하려면 기본 제공 전자 필기장을 실행 합니다.

1. Sentinel Azure 포털에서 클릭 **Notebook** 기본 제공 전자 필기장의 하나를 선택 합니다.
  
   ![notebook을 선택 합니다.](./media/notebooks/select-notebook.png)

3. 클릭 **가져오기** Azure Notebooks 프로젝트로 GitHub 리포지토리를 복제 합니다.
   ![Notebook 가져오기](./media/notebooks/import1.png)
4. 각 노트북을 이용 하면 헌트 또는 조사를 수행 하는 단계를 안내 합니다. 모델, 라이브러리 및 기타 종속성 및 Azure Sentinel에 대 한 연결에 대 한 구성을 클릭 한 번 실행할 수 있도록 자동으로 가져옵니다. 모든 코드와 notebook을 실행 하는 데 필요한 라이브러리 미리 로드 됩니다. 즉시 구성 없이 Log Analytics 작업 영역에 대 한 전자 필기장 실행을 시작할 수 있습니다.

   ![리포지토리 가져오기](./media/notebooks/import2.png)

5. 탐색, 수정 하 고 제공 된 모든 샘플 notebook을 실행 합니다. 이러한 용도로 사용할 수 있습니다 빌딩 블록으로 다양 한 시나리오입니다.

   ![notebook을 선택 합니다.](./media/notebooks/import3.png)



## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel에서 notebook을 사용 하 여 찾거나 조사를 실행 하는 방법을 알아보았습니다. Azure에서 감시 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 합니다.

- [사전에 위협에 대 한 hunt](hunting.md)
- [책갈피를 사용 하 여 사냥 하는 동안 흥미로운 정보를 저장 합니다.](bookmarks.md)