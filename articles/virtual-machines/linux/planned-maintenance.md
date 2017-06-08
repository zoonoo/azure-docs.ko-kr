---
title: "Azure에서 Linux VM에 대한 계획된 유지 관리 | Microsoft Docs"
description: "Azure 계획된 유지 관리의 정의와 계획된 유지 관리가 Azure를 실행하는 Windows 가상 컴퓨터에 주는 영향에 대해 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
author: zivr
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 28c2fb5a67eca0c5ab2f0299bba7c11375e10558
ms.contentlocale: ko-kr
ms.lasthandoff: 04/03/2017


---

# <a name="planned-maintenance-for-linux-virtual-machines"></a>Linux 가상 컴퓨터에 대한 계획된 유지 관리 

Microsoft Azure는 가상 컴퓨터의 기반이 되는 호스트 인프라의 안정성, 성능 및 보안을 향상시키기 위해 전 세계적으로 주기적인 업데이트를 수행합니다. 이러한 업데이트는 호스팅 환경의 소프트웨어 구성 요소(OS, 하이퍼바이저 및 호스트에 배포되는 다양한 에이전트) 패치, 네트워킹 구성 요소를 업그레이드, 하드웨어서비스 해제까지 다양합니다.

이러한 업데이트 중 대다수는 호스트된 가상 컴퓨터나 클라우드 서비스에 영향을 미치지 않고 수행됩니다.

그러나 업데이트가 호스트된 가상 컴퓨터에 영향을 미치는 경우가 있습니다.

-   전체 VM 마이그레이션을 사용하는 VM 보존 유지 관리는 유지 관리 동안 가상 시스템이 재부팅되지 않는 업데이트 클래스를 나타냅니다.

-   재부팅이 필요하거나 호스트된 가상 컴퓨터로 다시 배포하는 VM 다시 시작 유지 보수

이 페이지에서는 Microsoft Azure에서 계획된 유지 관리를 수행하는 방법을 설명합니다. 계획되지 않은 이벤트(중단)에 대한 자세한 내용은 [가상 컴퓨터의 가용성 관리](../windows/manage-availability.md)를 참조하세요.
