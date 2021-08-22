---
title: Azure Red Hat OpenShift 문제 해결
description: Azure Red Hat OpenShift의 일반적인 문제 해결 및 확인
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 1b68a84b599f8dab071b54f566721ecd7118294e
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090604"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift의 문제 해결

이 문서에서는 Microsoft Azure Red Hat OpenShift 클러스터를 만들거나 관리하는 도중 발생하는 일반적인 문제에 대해 자세히 설명합니다.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>실패한 클러스터 만들기 재시도

`az` CLI 명령을 사용해 Azure Red Hat OpenShift 클러스터를 만드는 작업이 실패한 경우, 만들기를 다시 시도하면 계속 실패합니다.
`az openshift delete`를 사용해 실패한 클러스터를 삭제한 다음, 완전히 새로운 클러스터를 만드세요.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>숨겨진 Azure Red Hat OpenShift 클러스터 리소스 그룹

현재 Azure CLI(`az openshift create` 명령)로 자동 생성된 `Microsoft.ContainerService/openShiftManagedClusters` 리소스는 Azure Portal에서 숨겨져 있습니다. **리소스 그룹** 보기에서 **숨겨진 유형 표시** 를 선택하여 리소스 그룹을 표시합니다.

![포털의 숨겨진 유형 확인란 스크린샷](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>클러스터를 만들면 등록된 리소스 공급자를 찾을 수 없다는 오류가 발생

클러스터를 만들 때 `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview` 오류가 발생한다면 미리 보기를 사용하고 있는 상황이며, 이제 일반 공급 제품을 사용하기 위해 [Azure 가상 머신 예약 인스턴스를 구매](https://aka.ms/openshift/buy)해야 합니다. 예약을 하면 완전 관리형 Azure 서비스에 선불로 할인된 비용이 청구되어 비용이 절감됩니다. 예약 및 비용 절감에 대한 자세한 내용은 [*Azure 예약 항목*](../cost-management-billing/reservations/save-compute-costs-reservations.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- OpenShift 문제 해결에 대한 자세한 내용은 [Red Hat OpenShift 도움말 센터](https://help.openshift.com/)에 문의하세요.

- [Azure Red Hat OpenShift에 대한 질문과 대답](openshift-faq.yml)을 참조하세요.