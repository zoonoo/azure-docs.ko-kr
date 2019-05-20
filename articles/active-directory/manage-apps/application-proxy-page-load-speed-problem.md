---
title: 애플리케이션 프록시 애플리케이션을 로드하는 데 시간이 너무 오래 걸림 | Microsoft Docs
description: Azure AD 애플리케이션 프록시의 로드 성능 문제 해결 페이지
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac1182d719d7c90129115e1fadf94f4f86a28e8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782641"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>애플리케이션 프록시 애플리케이션을 로드하는 데 시간이 너무 오래 걸림

이 문서는 Azure AD 애플리케이션 프록시 애플리케이션을 로드하는 데 시간이 오래 걸리는 이유를 이해하는 데 도움이 됩니다.니다. 또한 이 문제를 해결하기 위해 수행할 수 있는 작업에 대해 설명합니다.

## <a name="overview"></a>개요
애플리케이션이 작동하지만 긴 대기 시간이 발생할 수 있습니다. 속도를 향상시키기 위해 네트워크 토폴로지를 약간 조정할 수 있습니다. 여러 토폴로지에 대한 평가는 [네트워크 고려 사항 문서](application-proxy-network-topology.md)를 참조하세요.

현재 네트워크 토폴로지 외에도 성능 튜닝을 위한 추가적인 권장 사항이 없습니다. 애플리케이션 프록시 서비스가 확장됨에 따라 물리적으로 더 가까운 데이터 센터가 제공될 수 있습니다. 더 가까워지면 대기 시간에 도움이 될 수 있습니다. Azure 데이터 센터 목록은 [대기 시간 테스트 페이지](http://www.azurespeed.com/Azure/Latency)를 참조하세요. 

애플리케이션 프록시 서비스를 포함하는 데이터 센터는 [커넥터 포트 테스트 도구](https://aadap-portcheck.connectorporttest.msappproxy.net/)에서 찾을 수 있습니다. 

## <a name="feedback-on-application-proxy-data-center-locations"></a>애플리케이션 프록시 데이터 센터 위치에 대한 피드백 
아직 애플리케이션 프록시가 포함되지 않은 Azure 데이터 센터가 있을 수 있지만 이로 인해 사용자의 대기 시간이 크게 향상됩니다. 데이터 센터 위치를 aadapfeedback@microsoft.com으로 보냅니다. Microsoft는 확장 계획에 대한 사용자의 의견을 활용합니다.

Microsoft는 대기 시간을 향상시키는 추가 기능을 개발하기 위해 노력하고 있습니다. 향상된 이러한 기능이 사용할 수 있는 즉시 설명서가 업데이트됩니다.

## <a name="next-steps"></a>다음 단계
[기존 온-프레미스 프록시 서버 작업](application-proxy-configure-connectors-with-proxy-servers.md)
