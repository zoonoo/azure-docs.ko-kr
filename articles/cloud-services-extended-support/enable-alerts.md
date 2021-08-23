---
title: Azure Portal을 사용하여 Cloud Services(추가 지원)에서 모니터링 사용
description: Azure Portal을 사용하여 Cloud Services(추가 지원) 인스턴스에 대해 모니터링 사용
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 32f140ae27501b9f4f3b66e5f5815b8f3f20b3ff
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114445114"
---
# <a name="enable-monitoring-for-cloud-services-extended-support-using-the-azure-portal"></a>Azure Portal을 사용하여 Cloud Services(추가 지원)에 대해 모니터링 사용

이 문서에서는 기존 Cloud Services(추가 지원) 배포에 대한 경고를 사용하도록 설정하는 방법을 설명합니다. 

## <a name="add-monitoring-rules"></a>모니터링 규칙 추가
1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 경고를 사용하도록 설정할 Cloud Services(추가 지원) 배포를 선택합니다. 
3. **경고** 블레이드를 선택합니다. 

    :::image type="content" source="media/enable-alerts-1.png" alt-text="Azure Portal에서 경고 탭이 선택된 이미지.":::

4. **새 경고** 아이콘을 선택합니다.
     :::image type="content" source="media/enable-alerts-2.png" alt-text="새 경고 추가 옵션이 선택된 이미지.":::

5. 추적하고자 하는 메트릭에 따라 원하는 조건 및 필요한 작업을 입력합니다. 개별 메트릭 또는 활동 로그에 따른 규칙을 정의할 수 있습니다. 

     :::image type="content" source="media/enable-alerts-3.png" alt-text="경고에 조건을 추가하는 위치를 보여주는 이미지.":::

     :::image type="content" source="media/enable-alerts-4.png" alt-text="신호 논리를 구성하는 모습을 보여주는 이미지.":::

     :::image type="content" source="media/enable-alerts-5.png" alt-text="작업 그룹 논리를 구성하는 모습을 보여주는 이미지.":::

6. 경고 설정을 완료하고 변경 사항을 저장하면 구성한 메트릭을 기반으로 시간이 지남에 따라 **경고** 블레이드가 채워지는 것을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계 
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.yml)을 검토합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.