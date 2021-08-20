---
author: cherylmc
ms.author: cherylmc
ms.date: 06/29/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: f0585ca5bc2a76cb7f969f8a599ee98152b872cf
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108240"
---
Azure Bastion을 사용하여 VM에 연결하는 경우 VM의 공용 IP 주소가 필요하지 않음 공용 IP 주소를 사용하지 않는 경우 VM에서 연결을 끊을 수 있습니다. VM에서 공용 IP 주소의 연결을 끊으려면 다음 단계를 사용합니다.

1. 가상 머신으로 이동하고 **네트워킹** 을 선택합니다. **NIC 공용 IP** 를 선택하여 공용 IP 주소 페이지를 엽니다.

   :::image type="content" source="./media/bastion-remove-ip/networking.png" alt-text="네트워킹 페이지의 스크린샷":::

1. VM의 **공용 IP 주소** 페이지에서 **분리** 를 선택합니다.

   :::image type="content" source="./media/bastion-remove-ip/disassociate.png" alt-text="VM의 공용 IP 주소 스크린샷":::

1. **예** 를 선택하여 네트워크 인터페이스에서 IP 주소를 분리합니다.

   :::image type="content" source="./media/bastion-remove-ip/disassociate-yes.png" alt-text="공용 IP 주소 분리의 스크린샷":::

1. IP 주소를 분리하면 공용 IP 주소 리소스를 삭제할 수 있습니다. 공용 IP 주소 리소스를 삭제하려면 리소스 그룹으로 이동하고 삭제하려는 IP 주소 리소스를 찾습니다. 그런 다음, **삭제** 를 선택하여 리소스를 삭제합니다.

   :::image type="content" source="./media/bastion-remove-ip/delete-resource.png" alt-text="공용 IP 주소 리소스 삭제의 스크린샷":::