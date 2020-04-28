---
title: Azure Red Hat OpenShift 문제 해결
description: Azure Red Hat OpenShift를 사용 하 여 일반적인 문제 해결
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76274921"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift에 대 한 문제 해결

이 문서에서는 Red Hat OpenShift 클러스터 Microsoft Azure를 만들거나 관리 하는 동안 발생 하는 몇 가지 일반적인 문제에 대해 자세히 설명 합니다.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>실패 한 클러스터 만들기 다시 시도

`az` CLI 명령을 사용 하 여 Azure Red Hat openshift 클러스터를 만드는 작업이 실패 하는 경우 만들기를 다시 시도 하면 계속 실패 합니다.
를 `az openshift delete` 사용 하 여 실패 한 클러스터를 삭제 한 다음 완전히 새로운 클러스터를 만듭니다.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>숨겨진 Azure Red Hat OpenShift 클러스터 리소스 그룹

현재 Azure CLI ( `Microsoft.ContainerService/openShiftManagedClusters` `az openshift create` 명령)에서 자동으로 생성 되는 리소스는 Azure Portal에서 숨겨집니다. **리소스 그룹** 보기에서 **숨겨진 유형 표시** 를 선택 하 여 리소스 그룹을 봅니다.

![포털에서 숨겨진 유형 확인란의 스크린샷](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>클러스터를 만들면 등록 된 리소스 공급자를 찾을 수 없는 오류가 발생 합니다.

클러스터를 만들 때 오류가 발생 하면 미리 보기 `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`의 일부 이며, 이제 일반 공급 제품을 사용 하기 위해 [Azure 가상 머신 예약 인스턴스를 구매](https://aka.ms/openshift/buy) 해야 합니다. 예약은 완전히 관리 되는 Azure 서비스에 대해 사전 지불 하 여 비용을 절감 합니다. 예약 및 비용 절감 방법에 대 한 자세한 내용은 [*Azure Reservations 항목*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- OpenShift 문제 해결에 대 한 자세한 내용은 [Red Hat Openshift 도움말 센터](https://help.openshift.com/) 를 참조 하세요.

- [Azure Red Hat OpenShift에 대 한 자주 묻는 질문](openshift-faq.md)에 대 한 답변을 찾습니다.
