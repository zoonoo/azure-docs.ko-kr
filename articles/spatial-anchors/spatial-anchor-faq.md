---
title: Azure Spatial Anchors에 대한 FAQ | Microsoft Docs
description: Azure Spatial Anchors는 HoloLens, iOS 및 Android 디바이스에서 디바이스 간 다중 사용자 혼합 현실 환경을 지원하는 관리형 클라우드 서비스 및 개발자 플랫폼입니다. 이러한 FAQ는 기술적 관점에서 서비스에 대한 질문을 다룹니다.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: cd004e1eb6a40648c52dd3b5aee0ec38bff8f9b3
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304179"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Azure Spatial Anchors에 대한 FAQ

Azure Spatial Anchors는 HoloLens, iOS 및 Android 디바이스에서 다중 사용자, 공간 인식 혼합 현실 환경을 지원하는 관리형 클라우드 서비스 및 개발자 플랫폼입니다.

자세한 내용은 [Azure Spatial Anchors 개요](overview.md)를 참조하세요.

## <a name="azure-spatial-anchors-product-faqs"></a>Azure Spatial Anchors 제품 FAQ

**Q: Azure Spatial Anchors에서 지원하는 디바이스는 어떻게 되나요?**

**A:** Azure Spatial Anchors를 사용하면 개발자가 HoloLens, ARKit 지원 iOS 디바이스, ARCore 지원 Android 디바이스에서 앱을 빌드할 수 있습니다. iOS와 Android의 경우 휴대폰과 태블릿이 모두 포함됩니다.

**Q: Azure Spatial Anchors를 사용하려면 클라우드에 연결해야 하나요?**

**A:** Azure Spatial Anchors는 현재 인터넷에 대한 네트워크 연결이 필요합니다. [피드백 사이트](https://feedback.azure.com/forums/919252-azure-spatial-anchors)에서 의견을 보내주세요.

**Q: Azure Spatial Anchors 연결에 대한 요구 사항은 무엇인가요?**

**A:** Azure Spatial Anchors는 Wi-Fi 및 모바일 광대역 연결에서 작동합니다.

**Q: Azure Spatial Anchors에서 앵커를 정확히 찾으려면 어떻게 해야 하나요?**

**A:** 조명 조건, 환경 내의 개체 및 앵커가 배치되는 표면까지 앵커를 정확히 찾는 데 영향을 주는 요인은 다양합니다. 정확도가 요구 사항을 충족하는지 여부를 확인하려면 사용하려는 위치를 나타내는 환경에서 앵커를 사용해 보세요. 정확도가 요구 사항을 충족하지 않는 환경이 발생하는 경우 [Azure Spatial Anchors의 로깅 및 진단](./concepts/logging-diagnostics.md)을 참조하세요.

**Q: 앵커를 만들고 찾는 데 걸리는 시간은 어떻게 되나요?**

**A:** 앵커를 만들고 찾는 데 필요한 시간은 네트워크 연결, 디바이스의 처리 및 부하, 특정 환경과 같은 여러 요인에 따라 달라집니다. 제조, 소매, 게임을 포함하여 많은 산업에서 애플리케이션을 구축하는 고객이 있으며, 이 서비스를 통해 해당 시나리오에 대한 사용자 환경을 향상시킬 수 있음을 보여 주고 있습니다.

## <a name="privacy-faq"></a>개인 정보 보호 FAQ

**Q: 내 애플리케이션에서 Spatial Anchor를 어딘가에 배치하면 모든 앱에서 액세스할 수 있나요?**

**A:** 앵커는 Azure 계정으로 격리됩니다. 계정에 대한 액세스 권한을 부여한 앱만 계정 내의 앵커에 액세스할 수 있습니다.

**Q: Azure Spatial Anchors를 사용하는 경우 서비스에 전송되고 저장되는 환경에 대한 정보는 무엇인가요? 환경 관련 사진이 전송되고 저장되나요?**

**A**: 앵커를 만들거나 찾을 때 환경 관련 사진이 디바이스에서 파생된 형식으로 처리됩니다. 이 파생된 형식은 서비스에 전송되고 저장됩니다.

투명성을 제공하기 위해 환경과 파생된 스파스 지점 클라우드를 보여 주는 이미지는 아래와 같습니다. 지점 클라우드는 서비스에 전송되고 저장되는 환경의 기하학적 표현을 보여 줍니다. 스파스 지점 클라우드의 각 지점에 대해 해당 지점의 시각적 특성의 해시를 전송하고 저장합니다. 해시는 픽셀 데이터에서 파생되지만 포함되지 않습니다.

Azure Spatial Anchors는 [Azure 서비스 계약 및 조건](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9) 및 [Microsoft 개인정보처리방침](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409)을 준수합니다.

![환경 및 파생된 해당 스파스 지점 클라우드](./media/sparse-point-cloud.png)
*그림 1: 환경 및 파생된 해당 스파스 지점 클라우드*


**Q: 진단 정보를 Microsoft로 보낼 수 있는 방법이 있나요?**

**A**: 예. Azure Spatial Anchors에는 개발자가 Azure Spatial Anchors API를 통해 옵트인하기 위해 선택할 수 있는 진단 모드가 있습니다. 예를 들어 이 모드는 앵커를 예상대로 만들고 찾을 수 없는 환경이 발생하는 경우에 유용합니다. 디버그에 도움이 되는 정보가 포함된 진단 보고서를 제출할 수 있는지 물어볼 수 있습니다. 자세한 내용은 [Azure Spatial Anchors의 로깅 및 진단](./concepts/logging-diagnostics.md)을 참조하세요.

## <a name="availability-and-pricing-faqs"></a>가용성 및 가격 FAQ

**Q: SLA를 제공하나요?**

**A:** Azure 서비스 표준이므로 99.9%가 넘는 가용성을 목표로 합니다. Azure Spatial Anchors는 현재 미리 보기에 있으며, 이에 따라 [미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)이 적용됩니다.

**Q: 내 앱은 Azure Spatial Anchors를 사용하여 앱 스토어에 게시할 수 있나요? Azure Spatial Anchors는 중요 업무용 프로덕션 시나리오에 사용할 수 있나요?**

**A:** Azure Spatial Anchors는 현재 미리 보기에 있으며, 이 기간 동안 앱을 개발하고, 제품에 대한 [피드백을 제공](https://feedback.azure.com/forums/919252-azure-spatial-anchors)하며, 프로덕션 배포를 계획하는 것이 좋습니다.

GA(일반 공급) 날짜는 곧 발표할 예정입니다.

**Q: 적절한 제한 한도가 있나요?**
 
**A**: 예, 제한 한도가 있습니다.  이러한 문제는 일반적인 애플리케이션 개발 및 테스트에서 해결하지 못할 것입니다. 프로덕션 배포의 경우 고객의 높은 확장성 요구 사항을 지원할 준비가 되어 있습니다. 검토를 위해 [문의해 주세요](mailto:azuremrs@microsoft.com). 이 미리 보기 단계에서는 계층화 및 가격 책정 구조를 아직 발표하지 않았지만 곧 발표할 것입니다.

**Q: Azure Spatial Anchors를 사용할 수 있는 지역은 어떻게 되나요?**

**A:** Azure Spatial Anchors 계정은 현재 Azure 미국 동부 2 지역에서 만들 수 있습니다. 즉 이 서비스에서 지원하는 컴퓨팅과 스토리지가 모두 이 지역에 있습니다. 다시 말하면, 클라이언트의 위치에는 제한이 없습니다. 서비스의 지역 가용성은 나중에 모든 Azure 주 지역으로 확장됩니다.

**Q: Azure Spatial Anchors에 대해 요금을 부과하는가요? 혹은 청구되나요?**

**A:** 미리 보기 동안의 가격 책정에 대한 자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/spatial-anchors/)에서 확인할 수 있습니다.

## <a name="technical-faqs"></a>기술 FAQ

**Q: Azure Spatial Anchors는 어떻게 작동하나요?**

**A:** Azure Spatial Anchors는 혼합 현실/확대된 현실 추적기에 따라 달라집니다. 이러한 추적기는 카메라를 통해 환경을 인식하고, 공간을 이동하면서 6DoF(6자유도)로 디바이스를 추적합니다.

6DoF 추적기를 구성 요소로 지정하면 Azure Spatial Anchors를 통해 실제 환경의 특정 관심 지점을 "앵커" 지점으로 지정할 수 있습니다. 예를 들어 앵커를 사용하여 실제 환경의 특정 위치에서 콘텐츠를 렌더링할 수 있습니다.

앵커를 만들면 클라이언트 SDK에서 해당 지점 주변의 환경 정보를 캡처하여 서비스에 전송합니다. 다른 디바이스가 동일한 해당 공간에서 앵커를 찾는 경우 비슷한 데이터가 서비스에 전송됩니다. 해당 데이터는 이전에 저장된 환경 데이터와 대조됩니다. 그런 다음, 애플리케이션에서 사용하기 위해 디바이스 기준의 상대적 앵커 위치가 다시 전송됩니다.

**Q: Azure Spatial Anchors는 iOS 및 Android에서 ARKit 및 ARCore와 어떻게 통합되나요?**

**A:** Azure Spatial Anchors는 ARKit 및 ARCore의 기본 추적 기능을 활용합니다. 또한 iOS 및 Android용 SDK는 관리형 클라우드 서비스에서 앵커를 유지하고 앱에서 단순히 서비스에 연결하여 해당 앵커를 다시 찾을 수 있도록 하는 것과 같은 기능을 제공합니다.

**Q: Azure Spatial Anchors는 HoloLens와 어떻게 통합되나요?**

**A:** Azure Spatial Anchors는 HoloLens의 기본 추적 기능을 활용합니다. HoloLens에서 앱을 빌드하는 Azure Spatial Anchors SDK를 제공합니다. SDK는 기본 HoloLens 기능과 통합되며 추가 기능을 제공합니다. 이러한 기능에는 앱 개발자가 관리형 클라우드 서비스에서 앵커를 유지하고 앱에서 서비스에 연결하여 앵커를 다시 찾을 수 있도록 하는 기능이 포함됩니다.

**Q: Azure Spatial Anchors에서 지원하는 플랫폼과 언어는 어떻게 되나요?**

**A:** 개발자는 다음과 같이 해당 디바이스에 익숙한 도구와 프레임워크를 사용하여 Azure Spatial Anchors를 통해 앱을 빌드할 수 있습니다.

- HoloLens, iOS 및 Android의 Unity
- iOS의 Swift 또는 Object-C
- Android의 Java 또는 Android NDK
- HoloLens의 C++/WinRT

개발하려면 [여기](index.yml)서 시작하세요.

**Q: Unreal을 사용할 수 있나요?**

**A:** Unreal은 곧 지원할 예정입니다.

**Q: Xamarin을 사용할 수 있나요?**

**A:** 예. Xamarin SDK가 제공되지 않지만, 개발자는 Azure Spatial Anchors API와 통합하여 Xamarin 앱에서 Azure Spatial Anchors를 사용할 수 있습니다.
