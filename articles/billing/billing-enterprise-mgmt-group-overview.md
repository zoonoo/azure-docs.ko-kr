---
title: "Azure 관리 그룹으로 리소스 구성 - Azure | Microsoft Docs"
description: "관리 그룹 및 사용 방법에 대해 알아봅니다."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 18541c68b02ae1b59ae4a6a85122dff614c9978c
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---


# <a name="organize-your-resources-with-azure-management-groups"></a>Azure 관리 그룹으로 리소스 구성 

여러 구독이 있는 경우 "관리 그룹"이라는 컨테이너로 구성하여 구독 전반에서 액세스, 정책, 비용 및 규정 준수를 보다 편리하게 관리할 수 있습니다. 예를 들어 만들 수 있는 리소스 종류를 제한하는 정책을 관리 그룹에 적용할 수 있습니다.

> [!Note]
> 이 기능은 현재 비공개 미리 보기로 제공됩니다. [여기서 등록](https://aka.ms/MGPreviewSignup)하여 등록을 미리 보기에 가입합니다.   
 


관리 그룹은 계층 구조로 구성할 수 있습니다. 표시되는 구조는 있을 수 있는 관리 그룹 계층의 샘플 표현입니다.


![계층 구조 트리](media/billing-enterprise-mgmt-groups/tree.png)



## <a name="how-management-groups-are-related-to-your-azure-enterprise-enrollment"></a>관리 그룹이 Azure 엔터프라이즈 등록과 연관되는 방식

관리 그룹의 도입은 [Enterprise Portal](https://ea.azure.com) 기능을 [Azure Portal](https://portal.azure.com)로 전환하는 대대적인 과정의 한 단계입니다.

관리 그룹 구조는 Enterprise Portal에 정의된 대로 생성됩니다. 등록, 부서, 계정으로 구성되는 전체 계층 구조는 해당 관리 그룹에 매핑됩니다. 

현재 EA 구조가 관리 그룹 계층 구조에 매핑되는 방법은 다음과 같습니다. 

![계층 구조 트리](media/billing-enterprise-mgmt-groups/tree2.png)

다음 표에서는 Enterprise Portal의 사용자가 관리 그룹 계층 구조에 매핑되는 방식을 보여 줍니다.

|    EA 역할                                       |    매핑된 관리 그룹 노드의 역할    |    관리 그룹 노드에 대한 권한                                                          |
|--------------------------------------------------|--------------------------------------------------|----------------------------------------------------------------------------------------------------|
|    EA 관리자                              |    리소스 정책 참가자                   |    등록 노드 및 아래에 있는 비용을 보고, 리소스 정책을 관리하고, 계층 구조를 볼 수 있습니다.    |
|    읽기 전용 모드의 EA 관리자            |    청구 읽기 권한자                                |    등록 노드 및 아래에 있는 비용을 읽고 계층을 볼 수 있습니다.                              |
|    부서 관리자                      |    청구 읽기 권한자                                |    부서 노드 및 아래에 있는 비용을 읽고 계층을 볼 수 있습니다.                                 |
|    읽기 전용 모드의 부서 관리자    |    청구 읽기 권한자                                |    부서 노드 및 아래에 있는 비용을 읽고 계층을 볼 수 있습니다.                                 |
|    계정 소유자                                 |    리소스 정책 참가자                   |    계정 노드 및 아래에 있는 비용을 보고, 리소스 정책을 관리하고, 계층 구조를 볼 수 있습니다.       |




## <a name="view-management-groups-in-the-azure-portal"></a>Azure Portal에서 관리 그룹 보기

미리 보기 내에서 등록, 부서 또는 계정을 보려면 환영 메일의 링크를 사용해서 Azure Portal에 로그인합니다.   

![계층 구조](media/billing-enterprise-mgmt-groups/hierarchy.png)

### <a name="viewing-costs"></a>비용 보기 
관리 그룹의 세부 정보 화면에서 현재 월간 누계 비용을 볼 수 있습니다. 이러한 비용은 사용을 기반으로 하며 사전에 지불한 금액, 초과분, 포함된 수량, 조정 및 세금은 고려되지 않습니다. 등록에 해당하는 관리 그룹에 대한 비용 섹션에는 남은 약정이 표시됩니다.  

![등록 세부 정보](media/billing-enterprise-mgmt-groups/enrollment.png)

 "별도로 청구된 비용"은 Marketplace, 초과분 및 등록 약정을 벗어나는 기타 비용과 같이 별도 변경에 대한 월별 누적 비용입니다.  비용 분석에 대한 자세한 내용은 [Enterprise Portal](https://ea.azure.com)을 참조하세요. 

### <a name="enabling-access-to-costs"></a>비용에 대한 액세스 사용
비용이 표시되지 않는 경우 [엔터프라이즈 비용 보기 문제 해결](https://aka.ms/enableazurecosts) 문서를 참조하세요.  

### <a name="delays-between-the-enterprise-portal-and-azure-portal"></a>Enterprise Portal 및 Azure Portal 간 지연 
* 미리 보기 동안 Azure Portal 내에 표시되는 금액은 Enterprise Portal에 있는 값과 비교해서 지연될 수 있습니다. 이 문제는 일시적인 것이며 현재 해결 작업 중입니다.
* Enterprise Portal의 업데이트된 설정이 Azure Portal에 반영되는 데 몇 분 정도 걸립니다. 

## <a name="management-groups-have-a-relationship-with-your-directory"></a>관리 그룹과 디렉터리 간에 관계가 설정됨   
구독과 마찬가지로, 관리 그룹도 Azure AD와 신뢰 관계를 설정하고 있습니다. 관리 그룹 계층 구조는 사용자를 인증하기 위해 단일 디렉터리를 신뢰합니다. 관리 그룹 계층 구조와 연결된 모든 관리자는 같은 디렉터리에 속해야 합니다. 

등록 계층이 관리 그룹에 매핑될 때 단일 디렉터리와 신뢰 관계가 설정됩니다. 가능한 경우 등록의 사용자 계정과 연결된 기존 디렉터리가 선택되어 있습니다. 경우에 따라 새 디렉터리가 만들어지고 모든 기존 등록 사용자가 해당 디렉터리에 초대됩니다. 등록의 구독과 연결된 디렉터리는 영향을 받지 않습니다. 따라서 계층 구조는 구독과 다른 디렉터리에 만들어질 수 있습니다. 이 프로세스가 계층 구조 및 해당 구독 간의 탐색 환경에 영향을 미치는 방식을 [자세히 알아보세요](billing-enterprise-mgmt-grp-find.md).

## <a name="administering-your-management-groups"></a>관리 그룹 관리
Azure Portal 내의 관리 그룹은 미리 보기로 제공되며 이 초기 릴리스에서는 읽기 전용입니다. 업데이트를 수행하려면 Enterprise Portal로 이동합니다. 업데이트는 Azure Portal에 자동으로 반영됩니다. 편집 및 추가에 필요한 도움말을 보려면 Enterprise Portal 내의 설명서를 참조하세요.   

## <a name="policy-management"></a>정책 관리
리소스 관리자를 사용하면 리소스를 관리하기 위해 사용자 지정된 정책을 만들 수 있습니다. 관리 그룹을 사용하여 계층의 모든 수준에서 정책을 할당할 수 있으며, 리소스는 해당 정책을 상속합니다.  [자세히 알아보기](https://go.microsoft.com/fwlink/?linkid=858942)

> [!Note]
> 디렉터리가 달라지면 정책이 적용되지 않습니다. 



