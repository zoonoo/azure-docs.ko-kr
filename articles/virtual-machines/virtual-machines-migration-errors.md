---
title: "Azure Resource Manager 마이그레이션에 대한 클래식 중 일반 오류 | Microsoft Docs"
description: "이 문서에는 Azure 서비스 관리에서 Azure Resource Manager 스택으로의 IaaS 리소스 마이그레이션 중에 가장 일반적인 오류 및 해결 방법이 포함됩니다."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5bc03a1b-eb1c-438c-83d9-f0e9d61f1b6a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/13/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 345db9b2e45937ea45329acf780601e4e0fbd504


---
# <a name="common-errors-during-classic-to-azure-resource-manager-migration"></a>Azure Resource Manager 마이그레이션에 대한 클래식 중 일반 오류
이 문서에는 Azure 클래식 배포 모델에서 Azure Resource Manager 스택으로의 IaaS 리소스 마이그레이션 중에 가장 일반적인 오류 및 해결 방법이 포함됩니다.

## <a name="list-of-errors"></a>오류 목록
| 오류 문자열 | 해결 방법 |
| --- | --- |
| 내부 서버 오류 |어떤 경우에는 다시 시도하면 사라지는 일시적인 오류입니다. 계속 유지하는 경우 플랫폼 로그를 조사해야 한다면 [Azure 지원에 문의](../azure-supportability/how-to-create-azure-support-request.md)합니다. <br><br> **참고:** 인시던트가 지원 팀에서 추적되면 환경에서 의도하지 않은 결과가 발생할 수 있으므로 자체적으로 완화하려고 하지 않습니다. |
| 마이그레이션은 PaaS 배포(웹/작업자)이기 때문에 HostedService {hosted-service-name}에서 배포 {deployment-name}에 지원되지 않습니다. |배포가 웹/작업자 역할을 포함하는 경우에 발생합니다. 마이그레이션이 가상 컴퓨터에 대해서만 지원되므로 배포에서 웹/작업자 역할을 제거하고 마이그레이션을 다시 시도하세요. |
| 템플릿 {template-name} 배포에 실패했습니다. CorrelationId={guid} |마이그레이션 서비스의 백 엔드에서 Azure Resource Manager 템플릿을 사용하여 Azure Resource Manager 스택에서 리소스를 만듭니다. 템플릿이 idempotent이기 때문에 일반적으로 마이그레이션 작업을 안전하게 다시 시도하여 이 오류를 넘길 수 있습니다. 이 오류가 계속 지속되는 경우 [Azure 지원에 문의](../azure-supportability/how-to-create-azure-support-request.md)하고 CorrelationId를 제공하세요. <br><br> **참고:** 인시던트가 지원 팀에서 추적되면 환경에서 의도하지 않은 결과가 발생할 수 있으므로 자체적으로 완화하려고 하지 않습니다. |
| 가상 네트워크 {virtual-network-name}가 존재하지 않습니다. |새로운 Azure Portal에서 가상 네트워크를 만든 경우에 발생할 수 있습니다. 실제 가상 네트워크 이름은 "Group * <VNET name>" 패턴을 따릅니다. |
| HostedService {hosted-service-name}에서 VM {vm-name}에는 Azure Resource Manager에서 지원되지 않는 확장 {extension-name}이 포함되어 있습니다. 마이그레이션을 계속하기 전에 VM에서 제거하는 것이 좋습니다. |BGInfo 1.*과 같은 XML 확장은 Azure Resource Manager에서 지원되지 않습니다. 따라서 이러한 확장을 마이그레이션할 수 없습니다. 이러한 확장을 가상 컴퓨터에 설치된 대로 두면 마이그레이션을 완료하기 전에 자동으로 제거됩니다. |
| HostedService {hosted-service-name}에서 VM {vm-name}은 현재 마이그레이션에 지원되지 않는 VMSnapshot/VMSnapshotLinux 확장을 포함합니다. VM에서 제거하고 마이그레이션을 완료한 후에 Azure Resource Manager를 사용하여 다시 추가합니다. |가상 컴퓨터가 Azure Backup에 대해 구성되어 있는 시나리오입니다. 현재 지원되지 않는 시나리오이므로 https://aka.ms/vmbackupmigration에서 해결 방법을 따르세요. |
| HostedService {hosted-service-name}에서 VM {vm-name}에는 상태가 VM에서 보고되지 않는 확장 {extension-name}이 포함되어 있습니다. 따라서 이 VM은 마이그레이션할 수 없습니다. 확장 상태가 보고되고 있는지 확인하거나 VM에서 확장을 제거하고 마이그레이션을 다시 시도합니다. <br><br> HostedService {hosted-service-name}에서 VM {vm-name}에는 확장 {extension-name} 보고 처리기 상태 {handler-status}가 포함되어 있습니다. 따라서 VM은 마이그레이션할 수 없습니다. 보고되고 있는 확장 처리기 상태가 {handler-status}인지 확인하거나 VM에서 확장을 제거하고 마이그레이션을 다시 시도합니다. <br><br> HostedService {hosted-service-name}에서 VM {vm-name}의 VM 에이전트는 전반적인 에이전트 상태를 준비되지 않음으로 보고합니다. 따라서 VM은 마이그레이션할 수 있는 확장명이 있는 경우 마이그레이션되지 않을 수 있습니다. VM 에이전트가 전반적인 에이전트 상태를 준비됨으로 보고하는지 확인합니다. https://aka.ms/classiciaasmigrationfaqs를 참조하세요. |Azure 게스트 에이전트 및 VM 확장은 해당 상태를 채우기 위해 VM 저장소 계정에 대한 아웃바운드 인터넷 액세스가 필요합니다. 상태 실패의 일반적인 원인은 다음과 같습니다. <li> 인터넷에 대한 아웃바운드 액세스를 차단하는 네트워크 보안 그룹 <li> VNET에 온-프레미스 DNS 서버가 있고 DNS 연결이 손실된 경우 <br><br> 지원되지 않는 상태를 계속 표시하면 확장을 제거하여 이 검사를 건너뛰고 마이그레이션으로 이동할 수 있습니다. |
| 마이그레이션에는 여러 가용성 집합이 있기 때문에 HostedService {hosted-service-name}에서 배포 {deployment-name}에 지원되지 않습니다. |현재, 1 이하의 가용성 집합을 가진 호스티드 서비스만 마이그레이션할 수 있습니다. 이 문제를 해결하려면 해당 가용성 집합에서 추가 가용성 집합과 가상 컴퓨터를 다른 호스티드 서비스로 이동시킵니다. |
| HostedService에 가용성 집합이 있지만 마이그레이션에는 가용성 집합의 일부가 아닌 VM이 있기 때문에 HostedService {hosted-service-name}에서 배포 {deployment-name}에 지원되지 않습니다. |이 시나리오에 대한 해결 방법은 단일 가용성 집합에 있는 모든 가상 컴퓨터를 이동하거나 호스티드 서비스의 가용성 집합에서 모든 가상 컴퓨터를 제거하는 것입니다. |
| 저장소 계정/호스티드 서비스/가상 네트워크 {virtual-network-name}은 마이그레이션되는 프로세스이며 따라서 변경할 수 없습니다. |리소스에 대한 마이그레이션 작업 "준비"가 완료되고 리소스를 변경하는 작업이 트리거되면 이 오류가 발생합니다. "준비" 작업 후에 관리 평면에 대해 잠겨지기 때문에 리소스에 대한 변경 내용이 차단됩니다. 관리 평면 잠금을 해제하려면 마이그레이션을 완료하는 마이그레이션 작업을 "커밋"하거나 "준비" 작업을 롤백하는 "중단" 마이그레이션 작업을 실행할 수 있습니다. |
| RoleStateUnknown 상태인 VM {vm-name}이 있기 때문에 HostedService {hosted-service-name}에 대한 마이그레이션이 허용되지 않습니다. VM이 실행 중, 중지, 할당 취소 중지 상태 중 하나인 경우에만 마이그레이션이 허용됩니다. |VM은 HostedService에 대한 업데이트 작업 중에 재부팅, 확장 설치 등 일반적으로 발생하는 상태 전환이 발생할 수 있습니다. 마이그레이션을 시도하기 전에 HostedService을 완료하는 업데이트 작업을 수행하는 것이 좋습니다. |

## <a name="next-steps"></a>다음 단계
프로세스를 설명하는 마이그레이션 문서 목록은 다음과 같습니다.

* [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](virtual-machines-windows-migration-classic-resource-manager.md)
* [PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](virtual-machines-windows-ps-migration-classic-resource-manager.md)
* [CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](virtual-machines-linux-cli-migration-classic-resource-manager.md)




<!--HONumber=Nov16_HO3-->


