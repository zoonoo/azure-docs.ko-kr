---
title: Azure Cloud Services에 대한 크기 조정 구성(추가 지원)
description: Azure Cloud Services에 대한 크기 조정 옵션을 사용하는 방법(추가 지원)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: autoscale
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: dab3411319f40a111144a3e1c1f02f1b67d011b0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935654"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a>Azure Cloud Services에 대한 크기 조정 옵션 구성(추가 지원) 

Cloud Services(추가 지원) 배포를 확장 및 축소할 수 있도록 조건을 구성할 수 있습니다. 이러한 조건은 CPU 사용량, 디스크 부하 및 네트워크 부하를 기반으로 할 수 있습니다. 

클라우드 서비스 배포의 크기 조정을 구성하는 경우 다음 정보를 고려하세요.
- 크기 조정은 코어 사용량에 영향을 줍니다. 큰 역할 인스턴스는 더 많은 코어를 사용하며 구독의 코어 제한 내에서만 확장할 수 있습니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.
- 큐 메시징 임계값에 따라 크기 조정이 지원됩니다. 자세한 내용은 [Azure Queue Storage 시작](../storage/queues/storage-dotnet-how-to-use-queues.md)을 참조하세요.
- 클라우드 서비스(확장 지원) 애플리케이션의 고가용성을 보장하려면 둘 이상의 역할 인스턴스를 사용하여 배포해야 합니다.
- 사용자 지정 자동 크기 조정은 모든 역할이 **준비** 상태인 경우에만 발생할 수 있습니다.

## <a name="configure-and-manage-scaling"></a>크기 조정 구성 및 관리

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 크기 조정을 구성할 클라우드 서비스(추가 지원) 배포를 선택합니다. 
3. **크기 조정** 블레이드를 선택합니다. 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="Azure Portal에서 원격 데스크톱 옵션을 선택하는 것을 보여주는 이미지":::

4. 페이지에는 크기 조정을 구성할 수 있는 모든 역할의 목록이 표시됩니다. 구성할 역할을 선택합니다. 
5. 구성할 크기 조정 유형을 선택합니다.
    - **수동 크기 조정** 에서는 인스턴스의 절대 개수를 설정합니다.
        1. **수동 크기 조정** 을 선택합니다.
        2. 크기를 확장하거나 축소할 인스턴스 수를 입력합니다.
        3. **저장** 을 선택합니다.

        :::image type="content" source="media/enable-scaling-2.png" alt-text="Azure Portal에서 수동 크기 조정을 설정하는 방법을 보여주는 이미지":::

        4. 크기 조정 작업이 즉시 시작됩니다. 
        
    - **사용자 지정 자동 크기 조정** 을 사용하면 확장할 크기 정도를 제어하는 규칙을 설정할 수 있습니다. 
        1. **사용자 지정 자동 크기 조정** 을 선택합니다.
        2. 메트릭 또는 인스턴스 수에 따라 크기를 조정하려면 선택합니다.

        :::image type="content" source="media/enable-scaling-3.png" alt-text="Azure Portal에서 사용자 지정 자동 크기 조정을 설정하는 방법을 보여주는 이미지":::

        3. 메트릭을 기준으로 크기를 조정하는 경우 원하는 크기 조정 결과를 얻기 위해 필요한 규칙을 추가합니다.

        :::image type="content" source="media/enable-scaling-4.png" alt-text="Azure Portal에서 사용자 지정 자동 크기 조정 규칙을 설정하는 방법을 보여 주는 이미지":::

        4. **저장** 을 선택합니다.
        5. 규칙을 트리거하는 즉시 크기 조정 작업이 시작됩니다.
        
6. **크기 조정** 탭을 선택하여 배포에 적용된 기존 크기 조정 규칙을 확인하거나 조정할 수 있습니다.

    :::image type="content" source="media/enable-scaling-5.png" alt-text="Azure Portal에서 기존 크기 조정 규칙을 조정하는 것을 보여 주는 이미지":::

## <a name="next-steps"></a>다음 단계 
- Cloud Services(추가 지원)에 대한 [배포 필수 구성 요소](deploy-prerequisite.md)를 검토합니다.
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.md)을 검토합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.