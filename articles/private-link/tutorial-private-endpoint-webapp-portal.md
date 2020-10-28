---
title: '자습서: Azure Private Endpoint를 사용하여 웹앱에 연결'
titleSuffix: Azure Private Link
description: Azure Private Endpoint를 사용하여 웹앱에 개인적으로 연결하는 이 자습서를 시작합니다.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 10/19/2020
ms.openlocfilehash: 6d4d9fd901337b9c05c7d7d7f271974273e9fe37
ms.sourcegitcommit: 94ca9e89501e65f4dcccc3789249357c7d5e27e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92170068"
---
# <a name="tutorial-connect-to-a-web-app-using-an-azure-private-endpoint"></a>자습서: Azure Private Endpoint를 사용하여 웹앱에 연결

Azure Private 엔드포인트는 Azure에서 Private Link를 만드는 데 사용되는 기본 구성 요소입니다. 이를 사용하면 VM(가상 머신)과 같은 Azure 리소스에서 Private Link 리소스와 비공개로 통신할 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 가상 네트워크 및 베스천 호스트를 만듭니다.
> * 가상 머신을 만듭니다.
> * 웹앱을 만듭니다.
> * 프라이빗 엔드포인트를 만듭니다.
> * 웹앱 프라이빗 엔드포인트에 대한 연결을 테스트합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

> [!Note]
> Private Endpoint는 PremiumV2 계층/PremiumV3 계층 Windows 웹앱, Linux 웹앱 및 Azure Functions 프리미엄 플랜(탄력적 프리미엄 플랜이라고도 함)의 퍼블릭 지역에서 사용할 수 있습니다. 

## <a name="prerequisites"></a>전제 조건

* Azure 구독

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-virtual-network-and-bastion-host"></a>가상 네트워크 및 베스천 호스트 만들기

이 섹션에서는 가상 네트워크, 서브넷 및 베스천 호스트를 만듭니다. 

베스천 호스트는 가상 머신에 안전하게 연결하여 프라이빗 엔드포인트를 테스트하는 데 사용됩니다.

1. 화면의 왼쪽 위에서 **리소스 만들기 > 네트워킹 > 가상 네트워크** 를 차례로 선택하거나, 검색 상자에서 **가상 네트워크** 를 검색합니다.

2. **가상 네트워크 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | **설정**          | **값**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **프로젝트 세부 정보**  |                                                                 |
    | Subscription     | Azure 구독 선택                                  |
    | 리소스 그룹   | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** |                                                                 |
    | 속성             | **myVNet** 입력                                    |
    | 지역           | **미국 동부** 를 선택합니다. |

3. **IP 주소** 탭을 선택하거나 페이지 하단의 **다음: IP 주소** 단추를 선택합니다.

4. **IP 주소** 탭에서 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | IPv4 주소 공간 | **10.1.0.0/16** 입력 |

5. **서브넷 이름** 아래에서 **기본값** 이라는 단어를 선택합니다.

6. **서브넷 편집** 에서 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | 서브넷 이름 | **mySubnet** 을 입력합니다. |
    | 서브넷 주소 범위 | **10.1.0.0/24** 입력 |

7. **저장** 을 선택합니다.

8. **보안** 탭을 선택합니다.

9. **BastionHost** 에서 **사용** 을 선택합니다. 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | Bastion 이름 | **myBastionHost** 입력 |
    | AzureBastionSubnet 주소 공간 | **10.1.1.0/24** 입력 |
    | 공용 IP 주소 | **새로 만들기** 를 선택합니다. </br> **이름** 에 대해 **myBastionIP** 를 입력합니다. </br> **확인** 을 선택합니다. |


8. **검토 + 만들기** 탭을 선택하거나 **검토 + 만들기** 단추를 선택합니다.

9. **만들기** 를 선택합니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

이 섹션에서는 프라이빗 엔드포인트를 테스트하는 데 사용할 가상 머신을 만듭니다.


1. 포털의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **가상 머신** 을 선택하거나 검색 상자에 **가상 머신** 을 검색합니다.
   
2. **가상 머신 만들기** 의 **기본** 탭에서 값을 입력하거나 선택합니다.

    | 설정 | 값                                          |
    |-----------------------|----------------------------------|
    | **프로젝트 세부 정보** |  |
    | Subscription | Azure 구독 선택 |
    | 리소스 그룹 | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | **myVM** 을 입력합니다. |
    | Azure 지역 | **미국 동부** 를 선택합니다. |
    | 가용성 옵션 | **인프라 중복이 필요하지 않습니다** 를 선택합니다. |
    | 이미지 | **Windows Server 2019 Datacenter - Gen1** 을 선택합니다. |
    | Azure Spot 인스턴스 | **아니요** 를 선택합니다. |
    | 크기 | VM 크기를 선택하거나 기본 설정을 사용합니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 사용자 이름을 입력합니다. |
    | 암호 | 암호를 입력합니다. |
    | 암호 확인 | 암호를 다시 입력합니다. |

3. **네트워킹** 탭을 선택하거나 **다음: 디스크** , **다음: 네트워킹** 을 차례로 선택합니다.
  
4. [네트워킹] 탭에서 다음을 선택하거나 입력합니다.

    | 설정 | 값 |
    |-|-|
    | **네트워크 인터페이스** |  |
    | 가상 네트워크 | **myVNet** |
    | 서브넷 | **mySubnet** |
    | 공용 IP | **없음** 을 선택합니다. |
    | NIC 네트워크 보안 그룹 추가 | **기본**|
    | 공용 인바운드 포트 | **없음** 을 선택합니다. |
   
5. **검토 + 만들기** 를 선택합니다. 
  
6. 설정을 검토한 다음, **만들기** 를 선택합니다.

## <a name="create-web-app"></a>웹앱 만들기

이 섹션에서는 웹앱을 만듭니다.

1. 왼쪽 메뉴에서 **리소스 만들기** > **스토리지** > **웹앱** 을 차례로 선택하거나, 검색 상자에서 **웹앱** 을 검색합니다.

2. **웹앱 만들기** 페이지의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값                                          |
    |-----------------------|----------------------------------|
    | **프로젝트 세부 정보** |  |
    | Subscription | Azure 구독 선택 |
    | 리소스 그룹 | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** |  |
    | 속성 | **mywebapp** 을 입력합니다. 이름을 사용할 수 없는 경우 고유한 이름을 입력합니다. |
    | 게시 | **코드** 를 선택합니다. |
    | 런타임 스택 | **.NET Core 3.1(LTS)** 을 선택합니다. |
    | 운영 체제 | **Windows** 를 선택합니다. |
    | Azure 지역 | **미국 동부** 를 선택합니다. |
    | **App Service 계획** |  |
    | Windows 플랜(미국 동부) | **새로 만들기** 를 선택합니다. </br> **이름** 에서 **myServicePlan** 을 입력합니다. |
    | SKU 및 크기 | **크기 변경** 을 선택합니다. </br> **사양 선택기** 화면에서 **P2V2** 를 선택합니다. </br> **적용** 을 선택합니다. |
   
3. **검토 + 만들기** 를 선택합니다.

4. **만들기** 를 선택합니다.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/create-web-app.png" alt-text="Azure Portal에서 웹앱 만들기의 기본 사항 탭" border="true":::

## <a name="create-private-endpoint"></a>프라이빗 엔드포인트 만들기

1. 왼쪽 메뉴에서 **모든 리소스** > **mywebapp** 을 차례로 선택하거나 만드는 중에 선택한 이름을 선택합니다.

2. 웹앱 개요에서 **설정** > **네트워킹** 을 차례로 선택합니다.

3. **네트워킹** 에서 **프라이빗 엔드포인트 연결 구성** 을 선택합니다.

4. **프라이빗 엔드포인트 연결** 화면에서 **+ 추가** 를 선택합니다.

5. **프라이빗 엔드포인트 추가** 화면에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Name | **mywebappendpoint** 를 입력합니다. |
    | Subscription | 구독을 선택합니다. |
    | 가상 네트워크 | **myVNet** 을 선택합니다. |
    | 서브넷 | **mySubnet** 을 선택합니다. |
    | 프라이빗 DNS 영역과 통합 | **예** 를 선택합니다. |

6. **확인** 을 선택합니다.
    

## <a name="test-connectivity-to-private-endpoint"></a>프라이빗 엔드포인트에 연결 테스트

이 섹션에서는 이전 단계에서 만든 가상 머신을 사용하여 프라이빗 엔드포인트에서 웹앱에 연결합니다.

1. 왼쪽 탐색 창에서 **리소스 그룹** 을 선택합니다.

2. **myResourceGroup** 을 선택합니다.

3. **myVM** 을 선택합니다.

4. **myVM** 에 대한 개요 페이지에서 **연결** 을 선택한 다음, **Bastion** 을 선택합니다.

5. 파란색 **Bastion 사용** 단추를 선택합니다.

6. 가상 머신 만들기에서 입력한 사용자 이름과 암호를 입력합니다.

7. 연결한 후 서버에서 Windows PowerShell을 엽니다.

8. `nslookup <webapp-name>.azurewebsites.net`를 입력합니다. **\<webapp-name>** 을 이전 단계에서 만든 웹앱의 이름으로 바꿉니다.  아래 표시된 것과 유사한 메시지가 표시됩니다.

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    웹앱 이름에 대해 **10.1.0.5** 의 개인 IP 주소가 반환됩니다.  이 주소는 이전에 만든 가상 네트워크의 서브넷에 있습니다.

9. 로컬 컴퓨터에서 웹 브라우저를 열고, 웹앱의 외부 URL인 **https://\<webapp-name>.azurewebsites.net** 을 입력합니다.

10. **403** 페이지가 표시되는지 확인합니다. 이 페이지는 웹앱이 외부에서 액세스할 수 없음을 나타냅니다.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-ext-403.png" alt-text="Azure Portal에서 웹앱 만들기의 기본 사항 탭" border="true":::

11. **myVM** 에 대한 베스천 연결에서 Internet Explorer를 엽니다.

12. 웹앱의 URL( **https://\<webapp-name>.azurewebsites.net** )을 입력합니다.

13. 기본 웹앱 페이지가 표시되는지 확인합니다.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-default-page.png" alt-text="Azure Portal에서 웹앱 만들기의 기본 사항 탭" border="true":::

18. **myVM** 에 대한 연결을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 다음 단계에 따라 가상 네트워크, 가상 머신 및 웹앱을 삭제합니다.

1. 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.

2. **myResourceGroup** 을 선택합니다.

3. **리소스 그룹 삭제** 를 선택합니다.

4. **리소스 그룹 이름 입력** 에서 **myResourceGroup** 을 입력합니다.

5. **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

Private Link 서비스를 만드는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [Private Link 서비스 만들기](create-private-link-service-portal.md)
