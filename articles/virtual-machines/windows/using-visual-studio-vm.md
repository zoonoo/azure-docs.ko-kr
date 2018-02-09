---
title: "Azure Virtual Machine에서 Visual Studio 사용 | Microsoft Docs"
description: "Azure Virtual Machine에서 Visual Studio를 사용합니다."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: sacalla
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.prod: vs-devops-alm
ms.date: 01/30/2018
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 813022f1778e2c7f3174e11192b845c2c33ad219
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a id="top"> </a> Azure의 Visual Studio 이미지
미리 구성된 Azure VM(Virtual Machine)에서 실행되는 Visual Studio를 사용하는 것이 아무 작업도 하지 않고 실행 중인 개발 환경으로 전환하는 가장 쉽고 빠른 방법입니다.  다양한 Visual Studio 구성이 적용된 시스템 이미지를 [Azure Marketplace](https://portal.azure.com/)에서 사용할 수 있습니다. VM을 부팅하기만 하면 됩니다.

Azure를 처음 사용하세요? [평가판 Azure 계정을 만듭니다](https://azure.microsoft.com/free).

## <a name="what-configurations-and-versions-are-available"></a>어떤 구성 및 버전을 사용할 수 있나요?
Azure Marketplace에서 가장 최근의 주 버전인 Visual Studio 2015 및 Visual Studio 2017에 대한 이미지를 찾습니다.  각 주 버전에 대해 처음에 출시된(즉, 'RTW') 버전과 "최신" 업데이트 버전이 표시됩니다.  이러한 서로 다른 버전 각각에 대해 Visual Studio Enterprise 및 Visual Studio Community Edition을 찾을 수 있습니다.

|               릴리스 버전              |          에디션            |    제품 버전    |
|:------------------------------------------:|:----------------------------:|:---------------------:|
| Visual Studio 2017 - 최신 버전(버전 15.5) |    Enterprise, Community     |     버전 15.5.3    |
|         Visual Studio 2017 - RTW           |    Enterprise, Community     |     버전 15.0.7    |
|   Visual Studio 2015 - 최신 버전(업데이트 3)   |    Enterprise, Community     | 버전 14.0.25431.01 |
|         Visual Studio 2015 - RTW           | 없음(서비스를 위해 만료됨) |          ---          |

> [!NOTE]
> Microsoft 서비스 정책에 따라 최초 릴리스(즉, 'RTW') 버전의 Visual Studio 2015는 서비스 만료되었습니다.  따라서 Visual Studio 2015 Update 3은 Visual Studio 2015 제품 라인에 대해 제공되는 유일하게 남은 버전입니다.

자세한 내용은 [Visual Studio 서비스 정책](https://www.visualstudio.com/en-us/productinfo/vs-servicing-vs)을 참조하세요.

## <a name="what-features-are-installed"></a>어떤 기능이 설치되나요?
각 이미지는 해당 Visual Studio 에디션에 대해 권장되는 기능 집합을 포함합니다.  일반적으로 설치에는 다음이 포함됩니다.

* 해당 워크로드의 권장되는 선택적 구성 요소를 포함하는 모든 사용 가능한 워크로드
* .NET 4.6.2 및 4.7.NET SDK, 대상 지정 팩 및 개발자 도구
* Visual F#
* Visual Studio용 GitHub 확장
* LINQ to SQL 도구

다음은 이미지를 빌드할 때 Visual Studio를 설치하는 데 사용하는 명령줄입니다.

   * vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
   * add Microsoft.Net.Component.4.7.SDK ^
   * add Microsoft.Net.Component.4.7.TargetingPack ^ 
   * add Microsoft.Net.Component.4.6.2.SDK ^
   * add Microsoft.Net.Component.4.6.2.TargetingPack ^
   * add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
   * add Microsoft.VisualStudio.Component.FSharp ^
   * add Component.GitHub.VisualStudio ^
   * add Microsoft.VisualStudio.Component.LinqToSql

이미지에 필요한 Visual Studio 기능에 포함되어 있지 않으면, 사용자 의견 도구(페이지 오른쪽 위 모서리에 있음)를 통해 해당 사용자 의견을 제공합니다.

## <a name="what-size-vm-should-i-choose"></a>어떤 VM 크기를 선택해야 하나요?
새 가상 컴퓨터를 프로비전하는 것은 간단하며, Azure는 모든 범위의 가상 머신 크기를 제공합니다.  하드웨어 구입과 마찬가지로, 성능 대비 가격을 고려할 수 있습니다.  Visual Studio는 강력한 다중 스레드 응용 프로그램이므로, 2개 이상의 프로세서와 7GB 메모리를 포함하는 VM 크기를 원할 수 있습니다.  최신 컴퓨터 크기에 대한 자세한 내용은 [Azure에서 Windows 가상 컴퓨터에 대한 크기](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes)를 참조하세요.

Azure를 사용할 경우 처음 선택한 상태를 반드시 유지할 필요는 없으며 VM 크기를 조정하여 처음과는 다르게 선택할 수 있습니다.  좀 더 적절한 크기를 사용하여 새 VM을 프로비전할 수도 있고, 기존 VM의 크기를 다른 기본 하드웨어에 맞게 조정할 수도 있습니다.  자세한 내용은 [Windows VM 크기 조정](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/resize-vm)을 참조하세요.

## <a name="after-i-get-the-vm-running-then-what"></a>VM이 실행되고 있으면 어떻게 해야 하나요?
Visual Studio는 Azure의 "BYOL(사용자 라이선스 필요)" 모델을 따릅니다.  따라서 전용 하드웨어의 설치와 비슷하게, 첫 번째 단계 중 하나는 Visual Studio 설치의 사용을 허가하는 것입니다.  Visual Studio 구독과 연결된 Microsoft 계정으로 등록하여 Visual Studio 잠금을 해제하거나, 초기에 구입한 제품 키를 사용하여 Visual Studio 잠금을 해제할 수 있습니다.  자세한 내용은 [Visual Studio에 로그인](https://docs.microsoft.com/en-us/visualstudio/ide/signing-in-to-visual-studio) 및 [Visual Studio 잠금 해제 방법](https://docs.microsoft.com/en-us/visualstudio/ide/how-to-unlock-visual-studio)을 참조하세요.

## <a name="after-i-build-out-the-dev-vm-how-do-i-save-capture-it-for-future-or-team-use"></a>개발 VM을 구축한 후에 나중에 사용하거나 팀에서 사용할 수 있게 저장(캡처)하려면 어떻게 하나요?

개발 환경의 스펙트럼은 방대하며, 좀 더 복잡한 환경을 구축하게 되면 실질적인 비용이 발생합니다.  그러나 사용자 환경의 구성에 관계 없이, Azure는 완벽하게 구성된 VM을 사용자 자신 및/또는 팀의 다른 멤버가 향후 사용할 수 있도록 '기본 이미지'로 저장/캡처하여 투자를 쉽게 보존하도록 합니다.  그런 다음 새 VM을 부팅할 때, Marketplace 이미지가 아닌 기본 이미지에서 프로비전하면 됩니다.

빠르게 요약하자면, 실행 중인 VM을 sysprep 및 종료하고, Azure Portal의 UI를 통해 VM을 이미지로 *캡처(그림 1)*해야 합니다.  Azure에서는 선택한 저장소 계정의 이미지를 포함하는 `.vhd` 파일을 저장합니다.  그러면 새 이미지는 구독의 리소스 목록에서 이미지 리소스로 표시됩니다.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal’s UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(그림 1) Azure Portal의 UI를 통해 이미지를 캡처합니다.*</center>

자세한 내용은 [이미지에 VM 캡처](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/capture-image-resource)를 참조하세요.

  **미리 알림:** VM을 sysprep하는 것을 잊지 마세요.  이 단계를 누락하면 Azure가 이미지에서 VM을 프로비전할 수 없습니다.

> [!NOTE]
> 이미지 저장 비용이 발생하지만, 증가된 비용은 VM이 필요한 팀의 각 멤버들이 VM을 처음부터 새로 재구축하는 데 드는 노동력에 비해 별로 크지 않을 것입니다.  예를 들어, 팀의 모든 멤버가 재사용할 수 있는 127GB 이미지를 만들고 1달 동안 저장하는 데는 몇 달러가 필요합니다.  그러나 이러한 비용은 각 직원이 개인적으로 사용하기 위해 적절히 구성된 개발 박스를 빌드하고 유효성을 검사하기 위해 투입하는 시간에 비하면 별로 크지 않습니다.

또한 개발 태스크나 기술에는 다양한 개발 구성 및 여러 컴퓨터 구성과 같은 더 큰 규모가 필요할 수 있습니다.  Azure DevTest Labs를 사용하여 '골든 이미지'의 생성을 자동화하는 _레시피_를 만들고 VM을 실행하는 팀에 대한 정책을 관리할 수 있습니다.  [개발자용 Azure DevTest Lab을 사용](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-developer-lab)하는 것이 DevTest Labs에 대한 자세한 내용을 확인하는 최선의 방법입니다.

## <a name="next-steps"></a>다음 단계
미리 구성된 Visual Studio 이미지에 대해 잘 알게 되었으므로 다음 단계는 새 VM을 만드는 것입니다.

* [Azure Portal을 통해 VM 만들기](quick-create-portal.md)
* [Windows 가상 머신 개요](overview.md)
