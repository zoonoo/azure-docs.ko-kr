---
title: "네트워크 구성 파일을 사용하여 가상 네트워크 구성"
description: "가상 네트워크를 만들거나 수정하기 위해 Azure 관리 포털로 네트워크 구성 파일을 내보내고 가져오는 작업에 대한 지침입니다. "
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8a29f94cf0b6f8da70a5a18dde1dcbdca68bf284


---
# <a name="configure-a-virtual-network-using-a-network-configuration-file"></a>네트워크 구성 파일을 사용하여 가상 네트워크 구성
Azure 관리 포털을 사용하거나 네트워크 구성 파일을 사용하여 VNet(가상 네트워크)을 구성할 수 있습니다.

## <a name="creating-and-modifying-a-network-configuration-file"></a>네트워크 구성 파일 만들기 및 수정
네트워크 구성 파일을 작성하는 가장 간단한 방법은 기존 가상 네트워크 구성에서 네트워크 설정을 내보낸 다음 가상 네트워크에 대해 구성할 설정을 포함하도록 파일을 수정하는 것입니다.

네트워크 구성 파일을 편집하려면 파일을 열고 적절히 변경한 다음 저장하면 됩니다. *xml* 편집기를 사용하여 네트워크 구성 파일을 변경할 수 있습니다. 

[네트워크 구성 파일 스키마 설정](https://msdn.microsoft.com/library/azure/jj157100.aspx)에 대한 지침을 엄격히 준수해야 합니다. 

Azure에서는 배포된 항목이 있는 서브넷을 **사용 중**인 것으로 간주합니다. 사용 중인 서브넷은 수정할 수 없습니다. 수정하기 전에 서브넷에 배포한 항목을 수정 중이지 않은 다른 서브넷으로 이동하세요.   [다른 서브넷으로 VM 또는 역할 인스턴스 이동](virtual-networks-move-vm-role-to-subnet.md)을 참조하세요.

## <a name="export-and-import-virtual-network-settings-using-the-management-portal"></a>관리 포털을 사용하여 가상 네트워크 내보내기 및 가져오기
PowerShell 또는 관리 포털을 사용하여 네트워크 구성 파일에 포함된 내보내기 네트워크 구성 설정을 가져올 수 있습니다. 다음은 관리 포털을 사용하여 내보내고 가져오는 데 유용한 지침입니다. 

### <a name="to-export-your-network-settings"></a>네트워크 설정을 내보내려면
내보내면 구독의 가상 네트워크에 대한 모든 설정이 .xml 파일에 기록됩니다. 

1. **관리 포털**에 로그인합니다.
2. 관리 포털의 **네트워크** 페이지 맨 아래에서 **내보내기**를 클릭합니다. 
3. **네트워크 구성 내보내기** 창에서 네트워크 설정을 내보낼 구독을 선택했는지 확인합니다. 그런 다음 오른쪽 아래에 있는 확인 표시를 클릭합니다. 
4. 메시지가 표시되면 *NetworkConfig.xml* 파일을 선택한 위치에 저장합니다.

### <a name="to-import-your-network-settings"></a>네트워크 설정을 가져오려면
1. **관리 포털**의 왼쪽 아래에 있는 탐색 창에서 **새로 만들기**를 클릭합니다.
2. **Network Services** -> **Virtual Network** -> **구성 가져오기**를 클릭합니다.
3. **네트워크 구성 파일 가져오기** 페이지에서 네트워크 구성 파일을 찾은 후 **다음** 화살표를 클릭합니다.
4. **네트워크 빌드** 페이지의 화면에 변경하거나 만들 네트워크 구성 섹션을 보여 주는 정보가 표시됩니다. 변경 사항이 올바른 경우 확인 표시를 클릭하여 가상 네트워크를 계속 업데이트하거나 만듭니다. 




<!--HONumber=Nov16_HO3-->


