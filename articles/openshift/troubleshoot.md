---
title: 문제 해결 Azure 레드 햇 오픈 시프트
description: Azure Red Hat OpenShift에서 일반적인 문제 해결 및 해결
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274921"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Azure Red Hat 오픈시프트문제 해결

이 문서에서는 Microsoft Azure Red Hat OpenShift 클러스터를 만들거나 관리하는 동안 발생하는 몇 가지 일반적인 문제에 대해 자세히 설명합니다.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>실패한 클러스터 만들기 다시 시도

CLI 명령을 사용하여 Azure Red `az` Hat OpenShift 클러스터를 만드는 데 실패하면 만들기를 다시 시도하는 것은 계속 실패합니다.
실패한 `az openshift delete` 클러스터를 삭제한 다음 완전히 새 클러스터를 만드는 데 사용합니다.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>숨겨진 Azure Red Hat 오픈시프트 클러스터 리소스 그룹

현재 Azure `Microsoft.ContainerService/openShiftManagedClusters` CLI(명령)에`az openshift create` 의해 자동으로 생성되는 리소스는 Azure 포털에 숨겨져 있습니다. 리소스 **그룹** 보기에서 **숨겨진 형식 표시를** 선택하여 리소스 그룹을 봅니다.

![포털의 숨겨진 유형 확인란의 스크린샷](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>클러스터를 만들면 등록된 리소스 공급자를 찾지 못했다는 오류가 발생합니다.

클러스터를 만들면 미리 보기의 일부였고 이제 일반적으로 사용할 수 있는 제품을 사용하기 위해 [Azure 가상 컴퓨터 예약 인스턴스를 구입해야](https://aka.ms/openshift/buy) 하는 오류가 `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`발생합니다. 예약은 완전히 관리되는 Azure 서비스에 대해 선불로 비용을 절감합니다. 예약에 대해 자세히 알아보려면 [*Azure 예약이란 무엇이며,*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) 예약이 비용을 절감하는 방법에 대해 자세히 알아보십시오.

## <a name="next-steps"></a>다음 단계

- 오픈시프트 문제 해결에 대한 자세한 내용은 [Red Hat 오픈시프트 도움말 센터를](https://help.openshift.com/) 참조하십시오.

- [Azure Red Hat OpenShift에 대한 자주 묻는 질문에 대한](openshift-faq.md)답변을 찾아보십시오.
