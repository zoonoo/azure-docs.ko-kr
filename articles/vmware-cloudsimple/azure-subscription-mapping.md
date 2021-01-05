---
title: Azure 구독 매핑을 사용 하 여 리소스 풀 만들기
titleSuffix: Azure VMware Solution by CloudSimple
description: Azure 구독 매핑을 통해 사설 클라우드의 리소스 풀을 만드는 방법을 설명 합니다.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7be4a4c601d3f33972c1e52596ef623116dcadd4
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97897079"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Azure 구독 매핑을 사용 하 여 사설 클라우드에 대 한 리소스 풀 만들기
Azure 구독 매핑을 사용 하면 사용 가능한 vSphere 리소스 풀에서 사설 클라우드의 리소스 풀을 만들 수 있습니다. CloudSimple 포털에서 사설 클라우드에 대 한 Azure 구독을 보고 관리할 수 있습니다.

> [!NOTE]
> 리소스 풀을 매핑하면 모든 자식 리소스 풀도 매핑됩니다. 자식 리소스 풀이 이미 매핑된 경우 부모 리소스 풀을 매핑할 수 없습니다.

1. [CloudSimple 포털에 액세스](access-cloudsimple-portal.md)합니다.
2. **리소스** 페이지를 열고 **Azure 구독 매핑** 을 선택 합니다.  
3. **Azure 구독 매핑 편집** 을 클릭 합니다.  
4. 사용 가능한 리소스 풀을 매핑하려면 왼쪽에서 해당 리소스 풀을 선택 하 고 오른쪽 화살표를 클릭 합니다. 
5. 매핑을 제거 하려면 오른쪽에서 해당 매핑을 선택 하 고 왼쪽 화살표를 클릭 합니다. 

    ![Azure 구독](media/resources-azure-mapping.png)

6. **확인** 을 클릭합니다.
