---
title: Project Acoustics Azure Batch 계정 설정
titlesuffix: Azure Cognitive Services
description: 이 방법에서는 Project Acoustics Unity 및 Unreal engine 통합에 사용할 Azure Batch 계정을 설정 하는 방법에 대해 설명 합니다.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 8f0f726d9d23f20698d3510ad674331ad74fb703
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855088"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Project Acoustics Azure Batch 계정 설정
이 방법에서는 Project Acoustics Unity 및 Unreal engine 통합에 사용할 Azure Batch 계정을 설정 하는 방법에 대해 설명 합니다.

## <a name="get-an-azure-subscription"></a>Azure 구독 가져오기
Batch 및 Storage 계정을 설정하려면 [Azure 구독](https://azure.microsoft.com/free/)이 필요합니다. 처음 등록하는 경우 Azure는 시간이 제한된 몇 가지 무료 리소스와 $200 크레딧을 제공합니다.

## <a name="create-azure-batch-and-storage-accounts"></a>Azure Batch 및 Storage 계정 만들기
이제 [다음 지침](https://docs.microsoft.com/azure/batch/batch-account-create-portal)에 따라 Azure Batch 및 관련 Azure Storage 계정을 설정합니다.

Batch 및 Storage 계정에 대한 기본 옵션을 선택합니다.
  
  ![기본 설정을 표시 하 Azure Batch 새 계정 옵션의 스크린샷](media/new-batch-account-create.png)

  ![기본 설정을 표시 하 Azure Storage 새 계정 옵션의 스크린샷](media/batch-storage-account-create.png)

Azure에서 계정을 배포하는 데 몇 분 정도 걸립니다. 포털의 오른쪽 위 모서리에서 완료 알림을 찾습니다.
  
  ![Azure 계정 배포 알림 스크린샷](media/batch-accounts-deploy-notification.png)

이제 대시보드에 계정이 표시됩니다.
  
  ![Batch 및 Storage 계정을 보여 주는 Azure Portal 대시보드의 스크린샷](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Azure 자격 증명을 사용하여 음향 효과 준비 UI 설정
대시보드에서 Batch 계정 링크를 클릭한 다음, Batch 계정 페이지의 **키**를 클릭하여 자격 증명에 액세스합니다.
  
  ![키에 대 한 링크가 강조 표시 된 Azure Batch 계정의 스크린샷](media/batch-access-keys.png)

  ![액세스 키를 사용 하는 Azure Batch 계정 키 페이지의 스크린샷](media/batch-keys-info.png)

페이지의 **Storage 계정** 링크를 클릭하여 Azure Storage 계정 자격 증명에 액세스합니다.
  
  ![액세스 키를 사용 하는 Azure Storage 계정 키 페이지의 스크린샷](media/storage-keys-info.png)

[Unity 굽기 plugin](unity-baking.md) 또는 [unreal 굽기 플러그 인](unreal-baking.md)에 이러한 자격 증명을 입력 합니다.

## <a name="node-types-and-region-support"></a>노드 유형 및 지역 지원
Project Acoustics를 사용 하려면 모든 Azure 지역에서 지원 되지 않을 수 있는 Fsv2 및 H 시리즈 계산에 최적화 된 Azure VM 노드가 필요 합니다. [이 표](https://azure.microsoft.com/global-infrastructure/services)를 확인하여 Batch 계정에 대한 적절한 위치를 선택하세요.
![지역별 Azure Virtual Machines를 보여 주는 스크린샷](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>할당량 업그레이드
Azure Batch 계정은 계정 생성 시 프로비전되며, 20개의 컴퓨팅 코어로 제한됩니다. 굽기 시간을 단축 하기 위해이 제한을 늘릴 수 있습니다. 많은 노드의 acoustics 워크 로드를 장면에 있는 프로브 지점의 수까지 병렬 처리할 수 있기 때문입니다. 클릭 하 여 할당량 증가 요청할 수 있습니다 합니다 Azure Batch 포털 페이지에서 **할당량** 링크를 클릭하고 **할당량 증가 요청**을 클릭하여 할당량 증가를 요청할 수 있습니다.

![Azure 할당량 페이지의 스크린샷](media/azure-quotas.png)

## <a name="next-steps"></a>다음 단계
* Project Acoustics 플러그 인을 [Unity](unity-integration.md) 또는 [unreal](unreal-integration.md) 프로젝트에 통합

