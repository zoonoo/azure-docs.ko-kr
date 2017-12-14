---
title: "Azure 스택 개발 키트 배포 빠른 시작 | Microsoft Docs"
description: "Azure 스택 개발 키트를 배포 하는 방법에 알아봅니다"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/10/2017
ms.author: erikje
ms.custom: mvc
ms.openlocfilehash: 04742a587284cf72632360a9575a63a576da36e8
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2017
---
# <a name="azure-stack-development-kit-deployment-quickstart"></a>Azure Stack 개발 키트 배포 빠른 시작

*적용 대상: Azure 스택 개발 키트*

[Azure 스택 개발 키트](azure-stack-poc.md) 를 평가 하 여 Azure 스택 기능 및 서비스를 보여 줍니다. 배포할 수 있는 테스트 및 개발 환경입니다. 실행 해야 환경 하드웨어를 준비 하 고 (몇 시간 걸릴 됩니다) 일부 스크립트를 실행 합니다. 그 후 Azure 스택 관리 및 제공 테스트 관리자 및 사용자 포털 서명할 수 있습니다. 

1. [**하드웨어, 소프트웨어 및 네트워크 계획**](azure-stack-deploy.md)합니다. 개발 키트 (개발 키트 호스트)를 호스트 하는 컴퓨터 하드웨어, 소프트웨어 및 네트워크 요구 사항을 충족 해야 합니다. 또한 Azure Active Directory 또는 Active Directory Federation Services를 사용 하 여 간에 선택 해야 합니다. 설치 프로세스를 원활 하 게 실행 하 여 배포를 시작 하기 전에 이러한 필수 구성이 요소를 준수 해야 합니다. 

2. [**다운로드 하 고 배포 패키지를 압축**](azure-stack-run-powershell-script.md#download-and-extract-the-development-kit)합니다. 개발 키트 호스트 또는 다른 컴퓨터에 배포 패키지를 다운로드할 수 있습니다. 추출 된 배포 파일 다른 컴퓨터를 사용 하 여 개발 키트 호스트에 대 한 하드웨어 요구 사항을 줄일 수 있으므로 60GB의 사용 가능한 디스크 공간을 차지 합니다.

3. [**개발 키트 호스트 준비** ](azure-stack-run-powershell-script.md) 설치 관리자를 사용 하 여 합니다. 이 단계를 개발 키트 호스트 (가상 하드 드라이브에서 부팅 가능한 운영 체제와 Azure 스택 포함 된 파일을 설치 하는 데 사용) Cloudbuilder.vhdx 부팅 됩니다.

4. [**개발 키트 배포** ](azure-stack-run-powershell-script.md) 개발 키트 호스트에 있습니다.

5. Azure 스택 배포에서 Azure Active Directory를 실행 해야 [Azure와 함께 Azure 스택 등록](azure-stack-register.md) 수 있도록 [Azure 마켓플레이스 항목을 다운로드](azure-stack-download-azure-marketplace-item.md) Azure 스택에 합니다.

다음이 단계를 완료 한 후 포털 관리자와 사용자 모두 사용 하 여 개발 키트 환경을 해야 합니다. 그러면 할 수 [연결 하 고 로그인](azure-stack-connect-azure-stack.md) 포털에 있습니다. 리소스 공급자를 배포, 만들기 시작할 수 있습니다 [제공](azure-stack-key-features.md#regions-services-plans-offers-and-subscriptions), 하 고 Azure 스택 채우기 [마켓플레이스](azure-stack-marketplace.md)합니다.
