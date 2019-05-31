---
title: Azure Data Box Edge를 배포하기 위해 Azure Portal, 데이터 센터 환경을 준비하는 자습서 | Microsoft Docs
description: Azure Data Box Edge 배포에 대한 첫 번째 자습서에는 Azure Portal을 준비하는 작업이 포함됩니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: d7e66970db3397531c798bc37bf7c1f346e999bf
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924765"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>자습서: Azure Data Box Edge 배포 준비  


이 문서는 Azure Data Box Edge를 완전히 배포하는 데 필요한 배포 자습서 시리즈 중 첫 번째 자습서입니다. 이 자습서에서는 Data Box Edge 리소스를 배포하기 위해 Azure Portal을 준비하는 방법에 대해 설명합니다.

설치 및 구성 프로세스를 완료하려면 관리자 권한이 필요합니다. 포털 준비에는 10분 미만이 소요됩니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 새 리소스 만들기
> * 활성화 키 가져오기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.


### <a name="get-started"></a>시작하기

Data Box Edge를 배포하려면 다음 자습서를 지정된 순서대로 참조하세요.

| **#** | **단계** | **이러한 문서를 사용합니다.** |
| --- | --- | --- | 
| 1. |**[Data Box Edge에 대한 Azure Portal 준비](data-box-edge-deploy-prep.md)** |Data Box Edge 물리적 디바이스를 설치하기 전에 Data Box Edge 리소스를 만들고 구성합니다. |
| 2. |**[Data Box Edge 설치](data-box-edge-deploy-install.md)**|Data Box Edge 물리적 디바이스의 포장을 풀고, 랙에 탑재하고, 케이블을 연결합니다.  |
| 3. |**[Data Box Edge 연결, 설정 및 활성화](data-box-edge-deploy-connect-setup-activate.md)** |로컬 웹 UI에 연결하고 디바이스 설정을 완료한 다음, 디바이스를 활성화합니다. 디바이스에서 SMB 또는 NFS 공유를 설정할 준비가 되었습니다.  |
| 4. |**[Data Box Edge를 사용하여 데이터 전송](data-box-edge-deploy-add-shares.md)** |공유를 추가하고 SMB 또는 NFS를 통해 공유에 연결합니다. |
| 5. |**[Data Box Edge를 사용하여 데이터 변환](data-box-edge-deploy-configure-compute.md)** |디바이스에서 Edge 모듈을 구성하여 Azure로 이동할 때 데이터를 변환합니다. |

이제 Azure Portal 설치를 시작할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

다음은 Data Box Edge 리소스, Data Box Edge 디바이스 및 데이터 센터 네트워크에 대한 구성 필수 조건입니다.

### <a name="for-the-data-box-edge-resource"></a>Data Box Edge 리소스의 경우

시작하기 전에 다음 사항을 확인합니다.

- Data Box Edge 리소스에 대해 Microsoft Azure 구독이 활성화되어 있습니다. 종량제 구독은 지원되지 않습니다.
- 구독에 대한 소유자 또는 기여자 액세스 권한이 있습니다.
- Azure Active Directory Graph API에 대한 관리자 또는 사용자 액세스 권한이 있습니다. 자세한 내용은 [Azure Active Directory Graph API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-)를 참조하세요.
- 액세스 자격 증명이 있는 Microsoft Azure 저장소 계정이 있습니다.

### <a name="for-the-data-box-edge-device"></a>Data Box Edge 디바이스의 경우

물리적 디바이스를 배포하기 전에 먼저 다음 사항을 확인합니다.

- 배송 패키지에 포함된 안전 정보를 검토했습니다.
- 데이터 센터의 표준 19" 랙에 디바이스를 랙에 탑재할 수 있는 하나의 1U 슬롯이 있습니다. 
- 디바이스가 안전하게 있을 수 있는 평평하고 안정된 수평 작업대를 사용할 수 있습니다.
- 디바이스를 설치하려는 사이트에 독립적인 전원의 표준 AC 전원 디바이스가 있거나 UPS(무정전 전원 공급 디바이스)가 장착된 PDU(랙 전원 분배 디바이스)가 있습니다.
- 물리적 디바이스에 대한 액세스 권한이 있습니다.


### <a name="for-the-datacenter-network"></a>데이터 센터 네트워크의 경우

시작하기 전에 다음 사항을 확인합니다.

- 데이터 센터의 네트워크가 Data Box Edge 디바이스에 대한 네트워킹 요구 사항에 따라 구성되어 있습니다. 자세한 내용은 [Data Box Edge 시스템 요구 사항](data-box-edge-system-requirements.md)을 참조하세요.

- Data Box Edge가 정상 작동하기 위한 조건은 다음과 같습니다.

    - 디바이스가 업데이트 상태를 유지하기 위한 10Mbps의 최소 다운로드 대역폭.
    - 파일을 전송하기 위한 20Mbps의 최소 전용 업로드 및 다운로드 대역폭.

## <a name="create-a-new-resource"></a>새 리소스 만들기

물리적 디바이스를 관리할 수 있는 기존 Data Box Edge 리소스가 있으면 이 단계를 건너뛰고 [활성화 키 가져오기](#get-the-activation-key)로 이동합니다.

Data Box Edge 리소스를 만들려면 Azure Portal에서 다음 단계를 수행합니다.

1. Microsoft Azure 자격 증명을 사용하여 
    
    - URL [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인하거나
    - 또는 URL [https://portal.azure.us](https://portal.azure.us)에서 Azure Government 포털에 로그인합니다. 자세한 내용을 보려면 [포털을 사용하여 Azure Government에 연결](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal)로 이동합니다.

2. 왼쪽 창에서 **+ 리소스 만들기**를 선택합니다. **Data Box Edge/Data Box Gateway**를 검색합니다. **Data Box Edge/Data Box Gateway**를 선택합니다. **만들기**를 선택합니다.
3. Data Box Edge 디바이스에 사용하려는 구독을 선택합니다. Data Box Edge 리소스를 배포할 지역을 선택합니다. 이 릴리스에서는 미국 동부, 동남 아시아 및 서유럽을 선택할 수 있습니다. 

    디바이스를 배포하려는 지역에 지리적으로 가장 가까운 위치를 선택합니다. 지역에는 디바이스 관리용 메타데이터만 저장됩니다. 실제 데이터는 원하는 스토리지 계정에 저장할 수 있습니다. 
    
    **Data Box Edge** 옵션에서 **만들기**를 선택합니다.

    ![Data Box Edge 서비스 검색](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. **기본 사항** 탭에서 다음 **프로젝트 세부 정보**를 입력하거나 선택합니다.
    
    |설정  |값  |
    |---------|---------|
    |구독    |이 항목은 이전에 선택한 내용에 따라 자동으로 채워집니다. 구독은 청구 계정에 연결됩니다. |
    |리소스 그룹  |기존 그룹을 선택하거나 새 그룹을 만듭니다.<br>[Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)에 대해 자세히 알아봅니다.     |

4. 다음 **인스턴스 세부 정보**를 입력하거나 선택합니다.

    |설정  |값  |
    |---------|---------|
    |Name   | 리소스를 식별하기 위한 이름.<br>이름에는 문자, 숫자 및 하이픈을 포함하여 2~50자가 포함됩니다.<br> 이름은 문자 또는 숫자로 시작하고 끝납니다.        |
    |지역     |이 릴리스는 미국 동부, 동남 아시아 및 서유럽에 리소스를 배포할 수 있습니다. Azure Government를 사용하는 경우 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)에서 본 것처럼 모든 정부 지역을 사용할 수 있습니다.<br> 디바이스를 배포하려는 지역에 지리적으로 가장 가까운 위치를 선택합니다.|

    ![프로젝트 및 인스턴스 세부 정보](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. 완료되면 **다음: 배송 주소**를 선택합니다.

    - 디바이스가 이미 있는 경우 **I have a Data Box Edge device**(Data Box Edge 디바이스가 있습니다) 콤보 상자를 선택합니다.
    - 이 디바이스가 지금 주문하는 새 디바이스인 경우 연락처 이름, 회사, 디바이스를 배송할 주소, 연락처 정보를 입력합니다.

    ![새 디바이스의 배송 주소](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. 완료되면 **다음: 리뷰 + 만들기**를 클릭합니다.

7. **검토 + 만들기** 탭에서 **가격 책정 세부 정보**, **사용 약관** 및 리소스 세부 정보를 살펴봅니다. **I have reviewed the privacy terms**(개인정보처리방침을 검토했습니다) 콤보 상자를 선택합니다.

    ![Data Box Edge 리소스 세부 정보 및 개인정보처리방침 검토](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. **만들기**를 선택합니다.

리소스 생성에는 몇 분 정도가 소요됩니다. 리소스가 생성되고 배포된 후에는 알림이 표시됩니다. **리소스로 이동**을 선택합니다.

![Data Box Edge 리소스로 이동](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

주문이 완료되면 Microsoft에서 주문을 검토한 후 배송 세부 정보가 포함된 이메일을 통해 연락을 드립니다.

![Data Box Edge 주문 검토에 대한 알림](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>활성화 키 가져오기

Data Box Edge 리소스가 가동되어 실행되면 활성화 키를 가져와야 합니다. 이 키는 Data Box Edge 디바이스를 활성화하고 리소스와 연결하는 데 사용됩니다. 이제 Azure Portal에서 이 키를 얻을 수 있습니다.

1. 만든 리소스의 이름을 선택합니다. **개요**를 선택한 다음, **디바이스 설정**을 선택합니다.

    ![디바이스 설정 선택](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. **활성화** 타일에서 **키 생성**을 선택하여 활성화 키를 만듭니다. 복사 아이콘을 선택하여 키를 복사하고 나중에 사용할 수 있도록 저장합니다.

    ![활성화 키 가져오기](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - 활성화 키는 생성되고 3일 후에 만료됩니다.
> - 키가 만료된 경우 새 키를 만듭니다. 이전 키는 유효하지 않습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 Data Box Edge 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * 새 리소스 만들기
> * 활성화 키 가져오기

Data Box Edge를 설치하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Data Box Edge 설치](./data-box-edge-deploy-install.md)



