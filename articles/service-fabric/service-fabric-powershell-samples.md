---
title: Azure PowerShell 샘플 - Service Fabric | Microsoft Docs
description: Azure PowerShell 샘플 - Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/29/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: cae88e142c3bca15e837db7f084eef68434a78ca
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903258"
---
# <a name="azure-powershell-samples"></a>Azure PowerShell 샘플

다음 표에는 Service Fabric 클러스터, 애플리케이션 및 서비스를 만들고 관리하는 PowerShell 스크립트 샘플에 대한 링크가 제공됩니다.

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **클러스터 만들기** ||
| [클러스터 만들기(Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Azure Service Fabric 클러스터를 만듭니다. |
| **클러스터, 노드 및 인프라 관리** ||
| [애플리케이션 인증서 추가](./scripts/service-fabric-powershell-add-application-certificate.md) .| Key Vault에 대한 X509 인증서를 만들어 클러스터의 가상 머신 확장 집합에 배포합니다. |
| [클러스터 VM에서 RDP 포트 범위 업데이트](./scripts/service-fabric-powershell-change-rdp-port-range.md)|배포된 클러스터에서 클러스터 노드 VM의 RDP 포트 범위를 변경합니다.|
| [클러스터 노드 VM에 대한 관리자 사용자 및 암호 업데이트](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | 클러스터 노드 VM에 대한 관리자 사용자 및 암호를 업데이트합니다. |
| [Load Balancer에서 포트 열기](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | 특정 포트에서 인바운드 트래픽을 허용하도록 Azure Load Balancer에서 애플리케이션 포트를 엽니다. |
| [인바운드 네트워크 보안 그룹 규칙 만들기](./scripts/service-fabric-powershell-add-nsg-rule.md) | 특정 포트에서 클러스터에 대한 인바운드 트래픽을 허용하는 인바운드 네트워크 보안 그룹 규칙을 만듭니다. |
| **애플리케이션 관리** ||
| [애플리케이션 배포](./scripts/service-fabric-powershell-deploy-application.md)| 클러스터에 애플리케이션을 배포합니다.|
| [애플리케이션 업그레이드](./scripts/service-fabric-powershell-upgrade-application.md)| 애플리케이션을 업그레이드합니다.|
| [애플리케이션 제거](./scripts/service-fabric-powershell-remove-application.md)| 클러스터에서 애플리케이션을 제거합니다.|
