---
title: 포함 파일
description: 포함 파일
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: e3cb977871af2e6cd7a59dd48505090dd29e8a76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75541804"
---
1. [Azure Machine Learning Studio](https://ml.azure.com)에 로그인합니다.

1. 작업 영역을 엔터프라이즈 버전으로 업그레이드합니다.

    업그레이드 후 모든 시각적 인터페이스 실험은 디자이너의 파이프라인 초안으로 변환됩니다.
    
    > [!NOTE]
    > 시각적 인터페이스 웹 서비스를 실시간 엔드포인트로 변환하기 위해 엔터프라이즈 버전으로 업그레이드할 필요가 없습니다.
    
1. 작업 영역의 디자이너 섹션으로 이동하여 파이프라인 초안 목록을 확인합니다. 
    
    변환된 웹 서비스는 **끝점** > **실시간 끝점으로**이동하여 찾을 수 있습니다.

1. 파이프라인 초안을 선택하여 엽니다.

    변환 프로세스 중에 오류가 발생하면 문제를 해결하기 위한 지침과 함께 오류 메시지가 나타납니다. 

### <a name="known-issues"></a>알려진 문제

 다음은 수동으로 해결해야 하는 알려진 마이그레이션 문제입니다.

- **데이터 가져오기** 또는 **데이터 내보내기** 모듈
        
    실험에 **데이터 가져오기** 또는 **데이터 내보내기** 모듈이 있는 경우 데이터 저장소를 사용하도록 데이터 원본을 업데이트해야 합니다. 데이터 스토어를 만드는 방법에 대해 알아보려면 [Azure 저장소 서비스에서 데이터에 액세스하는 방법을](../articles/machine-learning/how-to-access-data.md)참조하세요. 클라우드 저장소 계정 정보는 사용자의 편의를 위해 **데이터 가져오기** 또는 **데이터 내보내기** 모듈의 주석에 추가되었습니다. 
      