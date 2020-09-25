---
title: 개인 끝점 구성 (미리 보기)
titleSuffix: Azure Machine Learning
description: Azure 개인 링크를 사용 하 여 가상 네트워크에서 Azure Machine Learning 작업 영역에 안전 하 게 액세스할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/21/2020
ms.openlocfilehash: 619960238125191e7bd4e702a49016c8fd58c847
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296657"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Azure Machine Learning 작업 영역에 대 한 Azure 개인 링크 구성 (미리 보기)

이 문서에서는 Azure Machine Learning 작업 영역에서 Azure 개인 링크를 사용 하는 방법에 대해 알아봅니다. Azure Machine Learning에 대 한 가상 네트워크 설정에 대 한 자세한 내용은 [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md) 를 참조 하세요.

> [!IMPORTANT]
> Azure Machine Learning 작업 영역에서 Azure 개인 링크를 사용 하는 것은 현재 공개 미리 보기 상태입니다. 이 기능은 다음 지역 에서만 사용할 수 있습니다.
>
> * **미국 동부**
> * **미국 중남부**
> * **미국 서부**
> * **미국 서부 2**
> * **캐나다 중부**
> * **동남 아시아**
> * **일본 동부**
> * **북유럽**
> * **동부 오스트레일리아**
> * **영국 남부**
>
> 이 미리 보기는 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 개인 링크를 사용 하면 개인 끝점을 사용 하 여 작업 영역에 연결할 수 있습니다. 프라이빗 엔드포인트는 가상 네트워크 내에 있는 일련의 개인 IP 주소입니다. 그런 다음 개인 IP 주소를 통해서만 발생 하도록 작업 영역에 대 한 액세스를 제한할 수 있습니다. 개인 링크를 사용 하면 데이터 exfiltration의 위험을 줄일 수 있습니다. 프라이빗 엔드포인트에 대한 자세한 내용은 [Azure Private Link](/azure/private-link/private-link-overview) 문서를 참조하세요.

> [!IMPORTANT]
> Azure 개인 링크는 작업 영역을 삭제 하거나 계산 리소스를 관리 하는 것과 같은 Azure 제어 평면 (관리 작업)에 영향을 주지 않습니다. 예를 들어 계산 대상을 생성, 업데이트 또는 삭제 합니다. 이러한 작업은 일반적인 공용 인터넷을 통해 수행 됩니다. Azure Machine Learning studio, Api (게시 된 파이프라인 포함)를 사용 하는 등의 데이터 평면 작업 또는 SDK는 개인 끝점을 사용 합니다.
>
> Mozilla Firefox를 사용 하는 경우 작업 영역에 대 한 개인 끝점에 액세스 하는 동안 문제가 발생할 수 있습니다. 이 문제는 HTTPS를 통한 DNS와 관련 된 것일 수 있습니다. 해결 방법으로 Google Chrome의 Microsoft Edge를 사용 하는 것이 좋습니다.

> [!TIP]
> Azure Machine Learning compute 인스턴스는 작업 영역 및 개인 끝점에서 사용할 수 있습니다. 이 기능은 현재 **미국 동부**, **미국 남부 중부** 및 **미국 서 부 2** 지역에서 공개 미리 보기로 제공 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

고객 관리 키를 사용 하 여 개인 링크를 사용 하도록 설정 된 작업 영역을 사용 하려는 경우 지원 티켓을 사용 하 여이 기능을 요청 해야 합니다. 자세한 내용은 [할당량 관리 및 늘리기](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)를 참조 하세요.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>개인 끝점을 사용 하는 작업 영역 만들기

> [!IMPORTANT]
> 현재는 새로운 Azure Machine Learning 작업 영역을 만들 때 전용 끝점을 사용 하도록 설정 하는 것만 지원 합니다.

의 템플릿은 개인 끝점을 사용 하 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) 여 작업 영역을 만드는 데 사용할 수 있습니다.

개인 끝점을 포함 하 여이 템플릿을 사용 하는 방법에 대 한 자세한 내용은 [Azure Resource Manager 템플릿을 사용 하 여 Azure Machine Learning에 대 한 작업 영역 만들기](how-to-create-workspace-template.md)를 참조 하세요.

## <a name="using-a-workspace-over-a-private-endpoint"></a>개인 끝점에서 작업 영역 사용

작업 영역에 대 한 통신은 가상 네트워크 에서만 허용 되므로 작업 영역을 사용 하는 모든 개발 환경은 가상 네트워크의 구성원 이어야 합니다. 예를 들어 가상 네트워크의 가상 머신이 있습니다.

> [!IMPORTANT]
> 일시적인 연결 중단을 방지 하려면 개인 링크를 사용 하도록 설정한 후 작업 영역에 연결 하는 컴퓨터에서 DNS 캐시를 플러시하는 것이 좋습니다. 

Azure Virtual Machines에 대 한 자세한 내용은 [Virtual Machines 설명서](/azure/virtual-machines/)를 참조 하세요.


## <a name="next-steps"></a>다음 단계

Azure Machine Learning 작업 영역을 보호 하는 방법에 대 한 자세한 내용은 [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md) 문서를 참조 하세요.
