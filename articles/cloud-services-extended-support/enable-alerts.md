---
title: Azure Portal을 사용 하 여 Cloud Services (확장 지원)에서 모니터링 사용
description: Azure Portal을 사용 하 여 Cloud Services (확장 지원) 인스턴스 모니터링 사용
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4591253e1a305632b7f41afe692f297d71366382
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744601"
---
# <a name="enable-monitoring-for-cloud-services-extended-support-using-the-azure-portal"></a>Azure Portal을 사용 하 여 Cloud Services (확장 지원)에 대 한 모니터링 사용

이 문서에서는 기존 클라우드 서비스 (확장 지원) 배포에 대 한 경고를 사용 하도록 설정 하는 방법을 설명 합니다. 

## <a name="add-monitoring-rules"></a>모니터링 규칙 추가
1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 경고를 사용 하도록 설정할 클라우드 서비스 (확장 지원) 배포를 선택 합니다. 
3. **경고** 블레이드를 선택 합니다. 

    :::image type="content" source="media/enable-alerts-1.png" alt-text="이미지에는 Azure Portal에서 경고 탭을 선택 하는 것이 표시 됩니다.":::

4. **새 경고** 아이콘을 선택 합니다.
     :::image type="content" source="media/enable-alerts-2.png" alt-text="이미지 새 경고 추가 옵션을 선택 합니다.":::

5. 추적에 관심이 있는 메트릭에 따라 원하는 조건 및 필요한 작업을 입력 합니다. 개별 메트릭 또는 활동 로그를 기준으로 규칙을 정의할 수 있습니다. 

     :::image type="content" source="media/enable-alerts-3.png" alt-text="이미지는 경고에 조건을 추가 하는 위치를 보여 줍니다.":::

     :::image type="content" source="media/enable-alerts-4.png" alt-text="이미지에서 신호 논리를 구성 하는 방법을 보여 줍니다.":::

     :::image type="content" source="media/enable-alerts-5.png" alt-text="이미지 작업 그룹 논리를 구성 하는 방법을 보여 줍니다.":::

6. 경고 설정을 완료 하 고 변경 내용을 저장 하 고 구성 된 메트릭에 따라 시간이 지남에 따라 **경고** 블레이드가 채워질 것을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계 
- Cloud Services (확장 지원)에 대 한 질문과 [대답](faq.md) 을 검토 합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용 하 여 클라우드 서비스 (확장 지원)를 배포 합니다.