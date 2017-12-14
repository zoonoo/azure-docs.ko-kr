---
title: "응용 프로그램 프록시 응용 프로그램을 로드하는 데 시간이 너무 오래 걸림 | Microsoft Docs"
description: "Azure AD 응용 프로그램 프록시의 로드 성능 문제 해결 페이지"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: d02c29fa6239cb36cb5fb95da4d325cca57952aa
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>응용 프로그램 프록시 응용 프로그램을 로드하는 데 시간이 너무 오래 걸림

이 문서를 통해 Azure AD 응용 프로그램 프록시 응용 프로그램을 로드하는 데 시간이 오래 걸리고 이 문제를 해결하기 위해 수행할 수 있는 작업을 이해할 수 있습니다.

## <a name="overview"></a>개요
응용 프로그램이 작동 중이지만 대기 시간이 길게 표시되는 경우 네트워크 토폴로지에서 속도를 개선하기 위해 고려할 수 있는 몇 가지 사소한 변경 사항이 있을 수 있습니다. 여러 토폴로지에 대한 평가는 [네트워크 고려 사항 문서](https://docs.microsoft.com/azure/active-directory/application-proxy-network-topology-considerations)를 참조하세요.

해당 고려 사항이 도움이 되지 않는 경우 안타깝게도 현재 성능을 조정하기 위한 추가 권장 사항이 없습니다. 응용 프로그램 프록시 서비스가 사용자에게 인접한 추가 데이터 센터로 확장되어 향상된 대기 시간을 직접 확인할 수 있습니다. Azure 데이터 센터의 전체 목록을 보려면 [대기 시간 테스트 페이지](http://www.azurespeed.com/Azure/Latency)를 참조하세요. 

응용 프로그램 프록시 서비스를 포함하는 데이터 센터는 [커넥터 포트 테스트 도구](https://aadap-portcheck.connectorporttest.msappproxy.net/)에서 찾을 수 있습니다. 

## <a name="feedback-on-application-proxy-data-center-locations"></a>응용 프로그램 프록시 데이터 센터 위치에 대한 피드백 
응용 프로그램 프록시를 아직 포함하지 않지만 사용자의 대기 시간을 개선할 수 있는 Azure 데이터 센터가 있을 수 있습니다. 데이터 센터가 <aadapfeedback@microsoft.com>에 위치하므로 확장한 대로 계획하는 데 피드백을 사용할 수 있습니다.

현재 긴 대기 시간을 표시하는 테넌트의 대기 시간을 개선할 수 있는 몇 가지 추가 기능으로 작업하고 있으므로 사용할 수 있는 문서를 공유해야 합니다.

## <a name="next-steps"></a>다음 단계
[기존 온-프레미스 프록시 서버 작업](application-proxy-working-with-proxy-servers.md)
