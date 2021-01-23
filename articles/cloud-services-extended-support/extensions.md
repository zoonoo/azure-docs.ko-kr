---
title: Cloud Services 확장 (확장 지원)
description: Cloud Services 확장 (확장 지원)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: c2eddf75f99b751773220ae677d66fe8c09abb0e
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744578"
---
# <a name="extensions-for-cloud-services-extended-support"></a>Cloud Services 확장 (확장 지원)

확장은 역할에 대 한 배포 후 구성 및 자동화 작업을 제공 하는 작은 응용 프로그램입니다. 예를 들어 원격 데스크톱 확장을 사용 하 여 클라우드 서비스 (확장 지원) 배포 중에 역할에서 원격 데스크톱 연결을 사용 하도록 설정할 수 있습니다.  

## <a name="remote-desktop-extension"></a>원격 데스크톱 확장

원격 데스크톱을 사용하면 Azure에서 실행 중인 역할의 데스크톱에 액세스할 수 있습니다. 원격 데스크톱 연결을 사용 하 여 실행 중인 응용 프로그램의 문제를 해결 하 고 문제를 진단할 수 있습니다.

서비스 정의 또는 원격 데스크톱 확장을 통해 원격 데스크톱 모듈을 포함 하 여 개발 중에 역할에서 원격 데스크톱 연결을 사용 하도록 설정할 수 있습니다. 

자세한 내용은 [Azure Portal에서 원격 데스크톱 구성](enable-rdp.md) 을 참조 하세요.

## <a name="windows-azure-diagnostics-extension"></a>Windows Azure 진단 확장

클라우드 서비스의 주요 성능 메트릭을 모니터링할 수 있습니다. 모든 클라우드 서비스 역할은 최소 데이터에 해당하는 CPU 사용량, 네트워크 사용량 및 디스크 사용률을 수집합니다. 클라우드 서비스의 역할에 적용 되는 Microsoft. Azure 진단 확장이 있는 경우 해당 역할은 추가 데이터 요소를 수집할 수 있습니다. 

기본 모니터링을 사용할 경우 역할 인스턴스의 성능 카운터 데이터는 3분 간격으로 샘플링되고 수집됩니다. 이 기본 모니터링 데이터는 스토리지 계정에 저장되지 않으며, 추가 비용도 부과되지 않습니다. 

고급 모니터링을 사용할 경우 5분, 1시간 및 12시간 간격으로 추가 메트릭이 샘플링되고 수집됩니다. 집계된 데이터는 스토리지 계정에 테이블로 저장되고, 10일 후에 삭제됩니다. 사용되는 스토리지 계정은 역할별로 구성됩니다. 따라서 역할마다 다른 스토리지 계정을 사용할 수 있습니다. 

[PowerShell](deploy-powershell.md) 또는 [ARM 템플릿을](deploy-template.md) 통해 Cloud Services (확장 지원)에 Windows Azure 진단 확장을 사용 하도록 설정할 수 있습니다.


## <a name="next-steps"></a>다음 단계 
- Cloud Services (확장 지원)에 대 한 [배포 필수 구성 요소](deploy-prerequisite.md) 를 검토 합니다.
- Cloud Services (확장 지원)에 대 한 질문과 [대답](faq.md) 을 검토 합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용 하 여 클라우드 서비스 (확장 지원)를 배포 합니다.