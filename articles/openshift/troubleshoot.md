---
title: Azure Red Hat OpenShift를 문제 해결 | Microsoft Docs
description: Azure Red Hat OpenShift를 사용 하 여 일반적인 문제 해결 및 문제 해결
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 7f2bdf643f12671bec3d0c087d8775844099fe9a
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306257"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift에 대 한 문제 해결

이 문서를 만들거나 Microsoft Azure의 Red Hat OpenShift 클러스터를 관리 하는 동안 발생 하는 몇 가지 일반적인 문제를 자세히 설명 합니다.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>실패 한 클러스터 만들기를 다시 시도

하는 경우 사용 하 여 클러스터 만들기는 Azure의 Red Hat OpenShift를 `az` CLI 명령이 실패, 만들기를 다시 시도 계속 실패 합니다.
사용 하 여 `az openshift delete` 실패 한 클러스터를 삭제 하려면 다음 완전히 새로운 클러스터를 만듭니다.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>숨겨진된 Azure Red Hat OpenShift 클러스터 리소스 그룹

현재는 `Microsoft.ContainerService/openShiftManagedClusters` Azure CLI에서 자동으로 만들어지는 리소스 (`az openshift create` 명령) Azure portal에서 숨겨집니다. 에 **리소스 그룹** 뷰를 검사 **숨겨진된 형식 표시** 리소스 그룹을 보려는.

![포털에서 숨겨진된 형식 확인란 스크린샷](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>결과 오류 없는 등록된 리소스 공급자가 있는 클러스터 만들기

오류로 클러스터 결과 만드는 경우 `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, 미리 보기의 일부가 되 고 이제 해야 [구매 Azure 가상 머신 예약 인스턴스](https://aka.ms/openshift/buy) 일반적으로 사용할 수 있는 제품을 사용 하도록 합니다. 예약을 감소에 완전히 관리 되는 Azure 서비스에 대 한 사전 지불 하 여 소비 합니다. 가리킵니다 [ *Azure 예약 이란* ](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) 예약 및 어떻게 이러한 비용을 절약 하는 방법에 대 한 자세한 내용을 보려면.

## <a name="next-steps"></a>다음 단계

- 시도 된 [Red Hat OpenShift 도움말 센터](https://help.openshift.com/) 자세한에 OpenShift 문제 해결에 대 한 합니다.

- 대답을 볼 [Azure Red Hat OpenShift에 대 한 질문과 대답](openshift-faq.md)합니다.
