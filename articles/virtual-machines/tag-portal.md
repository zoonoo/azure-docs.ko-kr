---
title: Azure Portal를 사용 하 여 VM에 태그를 만드는 방법
description: Azure Portal를 사용 하 여 가상 컴퓨터에 태그를 지정 하는 방법을 알아봅니다.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 7c3e779c152a967452f86b55f06d38076102085c
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594976"
---
# <a name="tagging-a-vm-using-the-portal"></a>포털을 사용 하 여 VM 태그 지정

이 문서에서는 포털을 사용 하 여 VM에 태그를 추가 하는 방법을 설명 합니다. 태그는 리소스 또는 리소스 그룹에 직접 배치할 수 있는 사용자 정의 키/값 쌍입니다. Azure는 현재 리소스 및 리소스 그룹당 최대 50 개의 태그를 지원 합니다. 태그를 만들 때 리소스에 배치하거나 기존 리소스에 추가할 수 있습니다.


1. 포털에서 VM으로 이동 합니다.
1. **Essentials** 에서 태그를 **추가 하려면 여기를 클릭** 하세요 .를 선택 합니다.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="VM 페이지의 Essentials 섹션 스크린샷":::

1. **이름** 및 **값** 에 대 한 값을 추가 하 고 **저장** 을 선택 합니다.

    :::image type="content" source="media/tag/key-value.png" alt-text="키 값 쌍을 태그로 추가 하는 페이지의 스크린샷":::



**다음 단계**

- Azure 리소스에 태그를 지정하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md) 및 [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md)을 참조하세요.
- 태그로 Azure 리소스의 사용을 관리하는 방법은 [Azure 청구서 이해](../cost-management-billing/understand/review-individual-bill.md) 및 [Microsoft Azure 리소스 소비에 대한 정보 얻기](../cost-management-billing/manage/usage-rate-card-overview.md)를 참조하세요.