---
title: Azure 템플릿 샘플 | Microsoft Docs
description: Virtual Network용 Azure 정책 템플릿 샘플입니다.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 8228da778e6cf6a874868ef0206092913908352d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694284"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>가상 네트워크용 Azure 정책 샘플 템플릿

다음 표에는 [Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 샘플이 포함되어 있습니다. 샘플은 [Azure Policy 샘플 리포지토리](https://github.com/Azure/azure-policy)에 있습니다.

| | |
|---|---|
|**네트워크**||
| [모든 NIC에서 NSG X](../governance/policy/samples/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 특정 네트워크 보안 그룹은 모든 가상 네트워크 인터페이스 함께 사용되어야 합니다. 사용할 네트워크 보안 그룹의 ID를 지정합니다. |
| [모든 서브넷에서 NSG X](../governance/policy/samples/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 특정 네트워크 보안 그룹이 모든 가상 서브넷과 함께 사용되어야 합니다. 사용할 네트워크 보안 그룹의 ID를 지정합니다. |
| [경로 테이블 없음](../governance/policy/samples/no-user-defined-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |가상 네트워크가 경로 테이블과 함께 배포되는 것을 금지합니다. |
| [VM 네트워크 인터페이스에 승인된 서브넷 사용](../governance/policy/samples/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 네트워크 인터페이스는 승인된 서브넷을 사용해야 합니다. 승인된 서브넷의 ID를 지정합니다. |
| [VM 네트워크 인터페이스에 승인된 VNet 사용](../governance/policy/samples/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 네트워크 인터페이스는 승인된 가상 네트워크를 사용해야 합니다. 승인된 가상 네트워크 ID를 지정합니다. |
|**모니터링**||
| [진단 설정 감사](../governance/policy/samples/audit-diagnostic-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 진단 설정이 지정된 리소스 형식에 대해 사용하도록 설정되지 않은 경우 감사합니다. 진단 설정이 사용되도록 설정되었는지 여부를 확인하려면 리소스 형식의 배열을 지정합니다. |
|**이름 및 텍스트 규칙**||
| [여러 이름 패턴 허용](../governance/policy/samples/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 리소스에 여러 이름 패턴 중 하나를 사용할 수 있습니다. |
| [유사 패턴 필요](../governance/policy/samples/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 리소스 이름 패턴에 대한 *유사* 조건을 충족하는지 확인합니다. |
| [일치 패턴 필요](../governance/policy/samples/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 리소스 이름이 지정된 이름 지정 패턴과 일치하는지 확인합니다. |
| [태그 일치 패턴 필요](../governance/policy/samples/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 태그 값이 텍스트 패턴과 일치하는지 확인합니다. |
|**태그**||
| [청구 태그 정책 이니셔티브](../governance/policy/samples/billing-tags-policy-initiative.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 비용 센터 및 제품 이름에 대해 지정된 태그 값이 필요합니다. 기본 제공 정책을 사용하여 필수 태그를 적용합니다. 태그에 대한 필수 값을 지정합니다.  |
| [리소스 그룹에 태그 및 해당 값 강제 적용](../governance/policy/samples/enforce-tag-on-resource-groups.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 리소스 그룹에 대한 태그 맟 값이 필요합니다. 필수 태그 이름 및 값을 지정합니다.  |
| [태그 및 해당 값 강제 적용](../governance/policy/samples/enforce-tag-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 지정된 태그 이름 및 값이 필요합니다. 강제 적용할 태그 이름 및 값을 지정합니다.  |
| [태그 및 기본값 적용](../governance/policy/samples/apply-tag-default-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 해당 태그를 제공하지 않으면 지정된 태그 이름 및 값을 추가합니다. 적용할 태그 이름 및 값을 지정합니다.  |
|**일반**||
| [허용되는 위치](../governance/policy/samples/allowed-locations.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 모든 리소스는 승인된 위치에 배포되어야 합니다. 승인된 위치 배열을 지정합니다.  |
| [허용되는 리소스 유형](../governance/policy/samples/allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 승인된 리소스 종류만 배포되도록 보장합니다. 허용되는 리소스 종류 배열을 지정합니다.  |
| [허용되지 않는 리소스 유형](../governance/policy/samples/not-allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 지정된 리소스 유형의 배포가 금지됩니다. 차단할 리소스 유형 배열을 지정합니다.  |