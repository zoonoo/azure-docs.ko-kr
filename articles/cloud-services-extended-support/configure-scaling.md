---
title: Azure Cloud Services에 대 한 크기 조정 구성 (확장 된 지원)
description: Azure Cloud Services에 대 한 크기 조정 옵션을 사용 하도록 설정 하는 방법 (확장 지원)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 482c11395ff5dbbf2dd5bb8100451c3442f2f333
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744517"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a>Azure Cloud Services를 사용 하 여 크기 조정 옵션 구성 (확장 지원) 

Cloud Services (확장 지원) 배포를 확장 및 축소할 수 있도록 조건을 구성할 수 있습니다. 이러한 조건은 CPU 사용량, 디스크 로드 및 네트워크 로드를 기반으로 할 수 있습니다. 

클라우드 서비스 배포의 크기 조정을 구성할 때 다음 정보를 고려 하십시오.
- 크기 조정은 코어 사용량에 영향을 줍니다. 더 큰 역할 인스턴스는 더 많은 코어를 사용 하 고 구독의 코어 제한 내 에서만 확장할 수 있습니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)을 참조 하세요.
- 큐 메시징 임계값에 따라 크기를 조정 하는 것이 지원 됩니다. 자세한 내용은 [Azure Queue Storage 시작](https://docs.microsoft.com/azure/storage/queues/storage-dotnet-how-to-use-queues)을 참조하세요.
- 클라우드 서비스 (확장 지원) 응용 프로그램의 고가용성을 보장 하려면 둘 이상의 역할 인스턴스를 사용 하 여 배포 해야 합니다.
- 사용자 지정 자동 크기 조정은 모든 역할이 **준비** 상태에 있는 경우에만 발생할 수 있습니다.

## <a name="configure-and-manage-scaling"></a>크기 조정 구성 및 관리

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 크기 조정을 구성 하려는 클라우드 서비스 (확장 지원) 배포를 선택 합니다. 
3. **크기 조정** 블레이드를 선택 합니다. 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="Azure Portal에서 원격 데스크톱 옵션을 선택 하는 이미지가 표시 됩니다.":::

4. 페이지에는 크기 조정을 구성할 수 있는 모든 역할의 목록이 표시 됩니다. 구성 하려는 역할을 선택 합니다. 
5. 구성할 확장 유형을 선택 합니다.
    - **수동 크기 조정** 에서는 인스턴스의 절대 개수를 설정 합니다.
        1. **수동 크기 조정** 을 선택 합니다.
        2. 규모를 확장 또는 축소 하려는 인스턴스 수를 입력 합니다.
        3. **저장** 을 선택합니다.

        :::image type="content" source="media/enable-scaling-2.png" alt-text="이미지는 Azure Portal에서 수동 크기 조정을 설정 하는 방법을 보여 줍니다.":::

        4. 크기 조정 작업은 즉시 시작 됩니다. 
        
    - **사용자 지정 자동 크기 조정을** 사용 하면 크기를 조정 하거나 크기를 조정 하는 규칙을 설정할 수 있습니다. 
        1. **사용자 지정 자동 크기 조정** 선택
        2. 메트릭 또는 인스턴스 수에 따라 크기를 조정 하려면 선택 합니다.

        :::image type="content" source="media/enable-scaling-3.png" alt-text="이미지는 Azure Portal에서 사용자 지정 자동 크기 조정을 설정 하는 방법을 보여 줍니다.":::

        3. 메트릭을 기준으로 크기를 조정 하는 경우 원하는 크기 조정 결과를 얻기 위해 필요한 규칙을 추가 합니다.

        :::image type="content" source="media/enable-scaling-4.png" alt-text="이미지는 Azure Portal에서 사용자 지정 자동 크기 조정 규칙을 설정 하는 방법을 보여 줍니다.":::

        4. **저장** 을 선택합니다.
        5. 규칙을 트리거하는 즉시 크기 조정 작업이 시작 됩니다.
        
6. **크기 조정** 탭을 선택 하 여 배포에 적용 된 기존 크기 조정 규칙을 확인 하거나 조정할 수 있습니다.

    :::image type="content" source="media/enable-scaling-5.png" alt-text="Azure Portal에서 기존 크기 조정 규칙을 조정 하는 이미지가 표시 됩니다.":::

## <a name="next-steps"></a>다음 단계 
- Cloud Services (확장 지원)에 대 한 [배포 필수 구성 요소](deploy-prerequisite.md) 를 검토 합니다.
- Cloud Services (확장 지원)에 대 한 질문과 [대답](faq.md) 을 검토 합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용 하 여 클라우드 서비스 (확장 지원)를 배포 합니다.
