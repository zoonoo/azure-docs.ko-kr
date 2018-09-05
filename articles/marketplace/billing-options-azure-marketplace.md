---
title: Azure Marketplace의 청구 옵션 | Azure
description: 게시자용 Azure Marketplace의 청구 옵션입니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: 67806563dcd67876d4d922c7f2c76379f7ad7222
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125516"
---
# <a name="billing-options-in-the-azure-marketplace"></a>Azure Marketplace의 청구 옵션

이 문서에서는 [Azure Marketplace](https://azuremarketplace.microsoft.com)에 사용할 수 있는 청구 옵션을 설명합니다.

## <a name="commercial-considerations-in-the-marketplace"></a>Marketplace 상용 고려 사항
Marketplace는 다음 목록 형식에 대한 수익을 공유하지 않습니다. 
*   나열
*   평가판
*   BYOL(사용자 라이선스 필요) 청구 모델을 사용하여 거래

Marketplace의 상점에 참여하기 위한 추가 비용은 없습니다.

자세한 내용은 [Microsoft Azure Marketplace 참가 정책](https://azure.microsoft.com/support/legal/marketplace/participation-policies)을 참조하세요.  

## <a name="pay-as-you-go-and-byol-billing-options"></a>종량제 및 BYOL 청구 옵션
게시 옵션으로 종량제 청구 모델을 사용할 경우 사용량을 기반으로 한 소프트웨어 라이선스 수익은 게시자와 Microsoft 간에 80%/20% 비율로 분배됩니다. 단일 제품은 종량제와 BYOL 청구 모델을 모두 사용하여 가격을 책정할 수 있습니다. 두 청구 모델은 제품 수준에서 별도 SKU로 동시에 존재합니다. Cloud 파트너 포털에서 제품의 청구 모델을 구성할 수 있습니다. 

다음 예제를 살펴보세요.
*   종량제 옵션을 사용하면 다음과 같은 결과가 나타납니다.
    | 라이선스 비용 | 시간당 $1.00 |
    |:--- |:--- |
    | Azure 사용량 비용(D1/1개 코어) | 시간당 $0.14 |
    | *Microsoft에서 고객에게 청구하는 요금* | *시간당 $1.14* |

    이 시나리오에서 Microsoft는 게시된 VM 이미지 사용에 대해 시간당 $1.14를 청구합니다.
    | Microsoft 청구 | 시간당 $1.14 |
    |:--- |:--- |
    | Microsoft는 라이선스 비용의 80%를 지불합니다. | 시간당 $0.80 |
    | Microsoft는 라이선스 비용의 20%를 유지합니다. | 시간당 $0.20 |
    | Microsoft는 Azure 사용량 비용을 유지합니다. | 시간당 $0.14 |

*   BYOL 옵션을 사용하면 다음과 같은 결과가 나타납니다.
    | 라이선스 비용 | 사용자가 라이선스 요금을 협상하고 청구합니다. |
    |:--- |:--- |
    | Azure 사용량 비용(D1/1개 코어) | 시간당 $0.14 |
    | *Microsoft에서 고객에게 청구하는 요금* | *시간당 $0.14* |

    이 시나리오에서 Microsoft는 게시된 VM 이미지 사용에 대해 시간당 $0.14를 청구합니다. 
    | Microsoft 청구 | 시간당 $0.14 |
    |:--- |:--- |
    | Microsoft는 Azure 사용량 비용을 유지합니다. | 시간당 $0.14 |
    | Microsoft은 라이선스 비용의 0%를 유지합니다. | 시간당 $0.00 |

## <a name="single-billing-and-payment-methods"></a>단일 청구 및 결제 방법
Marketplace에서 거래 게시 옵션을 사용할 때 얻을 수 있는 중요한 이점은 라이선스 비용 및 Azure 사용량이 고객에게 직접 단일 청구된다는 점입니다.

이 시나리오에서는 Microsoft가 게시자를 대신하여 요금을 청구 및 수금합니다. Microsoft 청구를 통해 사용자가 고객과의 자체 조달 관계를 만들 필요가 없어집니다. 단일 청구로 여러분의 시간과 리소스를 절약할 수 있습니다. 요금 징수 대신 판매 활동에 집중할 수 있다는 이점도 있습니다. 

## <a name="enterprise-agreement"></a>기업 계약  
Microsoft 기업계약 고객인 경우 기업계약을 사용하여 Microsoft 제품 대금을 결제할 수 있습니다. Azure 사용량을 비롯한 제품 대금을 청구 받을 수 있습니다. 기업계약을 이용한 결제는 3년 이상 소프트웨어 및 클라우드 서비스 라이선스를 허가하려는 조직을 위해 설계되었습니다. 선불 일시불 대신 할부로 결제합니다. 종량제 게시 옵션을 사용할 경우 소프트웨어 라이선스 비용 청구는 분기별 기업계약 초과분 청구 주기를 따릅니다.  

### <a name="monetary-commitment"></a>금액 약정
기업계약 고객인 경우 계약에 Azure를 추가할 수 있습니다. Azure에 선행 현금 약정을 체결하여 Azure를 계약에 추가합니다. 현금 약정은 1년 동안 사용됩니다. 약정에는 모든 Azure 서비스 사용 조합이 포함됩니다.

## <a name="next-steps"></a>다음 단계
[Azure Marketplace 및 AppSource 게시 가이드](./marketplace-publishers-guide.md)를 검토합니다.
