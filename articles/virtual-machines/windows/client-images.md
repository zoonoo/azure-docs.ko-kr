---
title: Azure에서 Windows 클라이언트 이미지 사용
description: Visual Studio 구독 혜택을 사용하여 개발/테스트 시나리오용으로 Azure에서 Windows 7, Windows 8 또는 Windows 10을 배포하는 방법
author: mimckitt
ms.subservice: imaging
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: mimckitt
ms.openlocfilehash: 7879e355573d2b9952728828bd3780094a919fe3
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869058"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>개발/테스트 시나리오용으로 Azure에서 Windows 클라이언트 사용
적절한 Visual Studio(이전의 MSDN) 구독이 있으면 Azure에서 개발/테스트 시나리오에 Windows 7, Windows 8 또는 Windows 10 Enterprise(x64)를 사용할 수 있습니다. 

프로덕션 환경에서 Windows 10을 실행 하려면 [다중 테 넌 트 호스팅 권한으로 Azure에서 windows 10을 배포 하는 방법](windows-desktop-multitenant-hosting-deployment.md)을 참조 하세요.


## <a name="subscription-eligibility"></a>구독 적격성
활성 Visual Studio 구독자 (Visual Studio 구독 라이선스를 획득 한 사용자)는 개발 및 테스트 목적으로 Windows 클라이언트 이미지를 사용할 수 있습니다. Windows 클라이언트 이미지는 자신의 하드웨어 또는 Azure virtual machines에서 사용할 수 있습니다.

특정 Windows 클라이언트 이미지는 Azure Marketplace에서 사용할 수 있습니다. 모든 종류의 제품 내에서 Visual Studio 구독자는 64 비트 Windows 7, Windows 8 또는 Windows 10 이미지를 준비 하 고 [만든](prepare-for-upload-vhd-image.md) 다음 [Azure에 업로드할](upload-generalized-managed.md)수도 있습니다.

## <a name="eligible-offers-and-client-images"></a>적격 제공 및 클라이언트 이미지
다음 표에서는 Azure Marketplace 통해 Windows 클라이언트 이미지를 배포할 수 있는 제품 Id에 대해 자세히 설명 합니다. Windows 클라이언트 이미지는 다음 제품에만 표시 됩니다. 

> [!NOTE]
> 이미지 제공은 Azure Marketplace의 **Windows 클라이언트** 아래에 있습니다. Visual Studio 구독자가 사용할 수 있는 클라이언트 이미지를 검색할 때 **Windows 클라이언트** 를 사용 합니다. 

| 제품 이름 | 제품 번호 | 사용 가능한 클라이언트 이미지 | 
|:--- |:---:|:---:|
| [종량제 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P | Windows 10 Enterprise Kn (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise Kn SP1 (x64) |
| [Visual Studio Enterprise(MPN) 구독자](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P | Windows 10 Enterprise Kn (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise Kn SP1 (x64) |
| [Visual Studio Professional 구독자](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P | Windows 10 Enterprise Kn (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise Kn SP1 (x64) |
| [Visual Studio Test Professional 구독자](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P | Windows 10 Enterprise Kn (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise Kn SP1 (x64) |
| [Visual Studio Premium with MSDN(혜택)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P | Windows 10 Enterprise Kn (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise Kn SP1 (x64) |
| [Visual Studio Enterprise 구독자](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P | Windows 10 Enterprise Kn (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise Kn SP1 (x64) |
| [Visual Studio Enterprise(BizSpark) 구독자](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P | Windows 10 Enterprise Kn (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise Kn SP1 (x64) |
| [Enterprise 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P | Windows 10 Enterprise Kn (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise Kn SP1 (x64) |

## <a name="check-your-azure-subscription"></a>Azure 구독 확인
제품 ID를 모르는 경우 Azure Portal를 통해 가져올 수 있습니다.  
- *구독* 창: Azure Portal의 ![ 제품 ID 세부 정보](./media/client-images/offer-id-azure-portal.png) 
- 또는 **청구** 를 클릭하고 구독 ID를 클릭합니다. 제품 ID가 *청구* 창에 표시됩니다. 
- Azure 계정 포털의 [' 구독 ' 탭](https://account.windowsazure.com/Subscriptions) 에서 제품 id를 볼 수도 있습니다. ![ azure 계정 포털의 제품 id 세부 정보](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>다음 단계
이제 [PowerShell](quick-create-powershell.md), [Resource Manager 템플릿](ps-template.md) 또는 [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)를 사용하여 VM을 배포할 수 있습니다.
