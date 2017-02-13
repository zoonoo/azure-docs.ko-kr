---
title: "Azure Marketplace for Azure Government 파트너 | Microsoft Docs"
description: "이 문서에서는 Azure Government의 응용 프로그램 개발에 대한 기능 및 지침을 비교합니다."
services: azure-government
cloud: gov
documentationcenter: 
author: tsingh
manager: asimm
ms.assetid: 7790d3c5-d0fa-4662-b4f0-a174cb7d6c9f
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: zakramer;tsingh;divacc
translationtype: Human Translation
ms.sourcegitcommit: 0839e8e57b2149e50d4512d28b1e57e6592be458
ms.openlocfilehash: 70821864520ff18ba8c64be0ea66376bccee7148


---
# <a name="azure-marketplace-for-azure-government"></a>Azure Marketplace for Azure Government
Azure Marketplace에 게시하는 기능에 관심이 있는 Azure Government 파트너는 이 문서에서 자세한 내용을 확인할 수 있습니다.

현재 Azure Government Marketplace에서는 BYOL VM 이미지 및 솔루션 템플릿이 지원됩니다. 모범 사례로, 솔루션 템플릿이 Azure 공용 및 Azure Government 클라우드 둘 다에서 작동되도록 하려면 Azure Government에 게시하기 전에 검토해야 합니다. VM 이미지만 게시하는 경우 아래의 게시 단계를 추가로 진행할 수 있습니다.

## <a name="pre-publishing-validation-for-solution-templates"></a>솔루션 템플릿에 대한 게시 전 유효성 검사

Azure Government에 솔루션 템플릿을 게시하기 전에 몇 가지 일반적인 모범 사례 영역을 확인하여 템플릿이 Azure 공용 클라우드 및 Azure Government 클라우드 둘 다에서 작동되는지 확인하는 것이 좋습니다.

1.  끝점이 Azure 공용 클라우드용 솔루션 템플릿으로 하드 코드되지 않아야 합니다. 이러한 끝점은 다른 Azure 환경에 적합하지 않습니다. 대신 유효한 끝점을 끌어오는 API 호출을 요청하도록 솔루션 템플릿을 수정합니다.  

  예제:

  잘못된 VHD URI(하드 코드됨) "uri": "[concat('https://', variables('storageAccountName'), '.blob.core.windows.net/',  '/osdisk.vhd')]",

  올바른 VHD URI(참조됨)

  "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'osdisk.vhd')]",

  수정된 구문은 템플릿이 모든 클라우드(Gov, Public Azure, AzureStack, China 등)에서 작동되도록 합니다.

2.  솔루션 템플릿에서 사용되는 리소스를 게시하는 Sovereign 클라우드에서 사용할 수 있는지 확인합니다.
Azure 및 API 버전의 RP

    포털에서 리소스 탐색기를 사용하여 Azure Government의 가용성을 확인합니다.

  1.    Azure Government Portal에 로그인
  2.    https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-supported-services#supported-regions에 나열된 단계에 따라 리소스 탐색기 시작

  가장은 일반적으로 사용되는 확장. Fairfax의 가용성  

  | 게시자/유형 | Fairfax에서 사용 가능한 버전 |
  | --- | --- |
  | Microsoft.OSTCExtensions/CustomScriptForLinux | 1; 1.1; 1.2.2.0; 1.3.0.2; 1.4.1.0; 1.5.2.0 |
  | Microsoft.Compute/CustomScriptExtension | 1.2; 1.3; 1.4; 1.7; 1.8 |
  | Microsoft.Azure.Extensions/DockerExtension | 사용할 수 없음 |
  | Microsoft.Azure.Extensions/CustomScript | 2.0.2 |
  | Microsoft.OSTCExtensions/LinuxDiagnostic | 2.0.9005; 2.1.9005; 2.2.9005; 2.3.9011 |
  | Microsoft.Powershell/DSC | 2.19.0.0 |

> [!NOTE]
> 허용되는 값 목록에 대해 위치를 추가하면 새 하위 지역이 추가될 때 정기적으로 업데이트해야 합니다.  


## <a name="publishing"></a>게시
> [!NOTE]
> 기존 Azure Certified Marketplace 파트너가 아닌 경우 계속하기 전에 [제품 게시 및 관리](../marketplace-publishing/marketplace-publishing-getting-started.md) 방법을 보여 주는 단계를 완료하세요.
>
>

### <a name="step-1"></a>1단계:
[Azure 게시](https://publish.windowsazure.com)에 로그인합니다.

### <a name="step-2"></a>2단계:
게시하려는 제품을 클릭합니다.

### <a name="step-3"></a>3단계:
**SKUS**를 클릭하고 **Azure Government Cloud** 상자를 클릭합니다.

> [!NOTE]
> BYOL(사용자 라이선스 필요) SKU만 지원됩니다.  이 옵션은 PayG(종량제) SKU로 사용할 수 없습니다.
>
>

![SKU 및 Azure Government Cloud 옵션이 있는 제품 페이지](./media/government-manage-marketplace-partner-1.png)

### <a name="step-4"></a>4단계:
**+ 인증 추가** 링크를 클릭하여 제품 인증에 대한 링크를 추가합니다.

![인증 추가 링크가 있는 제품 페이지](./media/government-manage-marketplace-partner-2.png)

### <a name="step-5"></a>단계
게시 포털에서 이미지를 테스트하려면 Microsoft Azure Government에 대한 [평가판 계정](https://azuregov.microsoft.com/trial/azuregovtrial)을 요청합니다.

미국 연방 정부, 주정부, 지방 정부 또는 소규모 조직을 지원하는 파트너 자격을 확인한 후 전자 메일을 통해 확인서를 발급합니다.  평가판 계정은 영업일 기준&3;-5일 이내에 사용할 수 있습니다.

### <a name="step-6"></a>6단계:
**게시**를 클릭하고 **스테이징으로 푸시**를 클릭합니다.

![게시 및 스테이징으로 푸시 옵션](./media/government-manage-marketplace-partner-3.png)

스테이징 제품에 대한 액세스 권한이 있는 허용 목록 구독을 입력하라는 메시지가 표시됩니다. 새로 얻은 Azure Government 평가판 계정에서 구독 ID를 입력합니다.

![제품에 액세스할 수 있는 구독 ID를 지정하라는 메시지](./media/government-manage-marketplace-partner-4.png)

### <a name="step-7"></a>7단계:
제품이 성공적으로 스테이징되면 Azure Government 평가판 계정을 사용하여 [Azure Portal](https://portal.azure.us)에 로그인하고 이미지를 테스트할 수 있습니다.

### <a name="step-8"></a>8단계:
평가판 구독을 사용하여 이미지를 검증한 후에는 **게시**를 클릭하고 프로덕션 이동 승인을 요청하여 제품을 공급할 수 있습니다.

![프로덕션 이동 승인 요청 명령](./media/government-manage-marketplace-partner-5.png)

## <a name="next-steps"></a>다음 단계
부가 정보 및 업데이트를 보려면 [Microsoft Azure Government 블로그](https://blogs.msdn.microsoft.com/azuregov/)를 구독하세요.



<!--HONumber=Dec16_HO3-->


