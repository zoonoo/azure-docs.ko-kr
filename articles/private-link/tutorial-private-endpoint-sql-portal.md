---
title: 자습서 - Azure Private Endpoint를 사용하여 Azure SQL 서버에 연결 - 포털
description: 이 자습서를 통해 Azure Portal을 사용하여 프라이빗 엔드포인트가 있는 Azure SQL 서버를 만드는 방법을 알아봅니다.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: def14cec9d010104876acaf9588560722dd98884
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145670"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-portal"></a>자습서 - Azure Private Endpoint를 사용하여 Azure SQL 서버에 연결 - Azure Portal

Azure Private 엔드포인트는 Azure에서 Private Link를 만드는 데 사용되는 기본 구성 요소입니다. 이를 사용하면 VM(가상 머신)과 같은 Azure 리소스에서 Private Link 리소스와 비공개로 통신할 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 가상 네트워크 및 베스천 호스트를 만듭니다.
> * 가상 머신을 만듭니다.
> * Azure SQL 서버 및 프라이빗 엔드포인트를 만듭니다.
> * SQL 서버 프라이빗 엔드포인트에 대한 연결을 테스트합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.


## <a name="create-a-virtual-network-and-bastion-host"></a>가상 네트워크 및 베스천 호스트 만들기

이 섹션에서는 가상 네트워크, 서브넷 및 베스천 호스트를 만듭니다. 

베스천 호스트는 가상 머신에 안전하게 연결하여 프라이빗 엔드포인트를 테스트하는 데 사용됩니다.

1. 화면의 왼쪽 위에서 **리소스 만들기 > 네트워킹 > 가상 네트워크** 를 차례로 선택하거나, 검색 상자에서 **가상 네트워크** 를 검색합니다.

2. **가상 네트워크 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | **설정**          | **값**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **프로젝트 세부 정보**  |                                                                 |
    | Subscription     | Azure 구독 선택                                  |
    | 리소스 그룹   | **CreateSQLEndpointTutorial-rg** 를 선택합니다. |
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
    | 리소스 그룹 | **CreateSQLEndpointTutorial** 을 선택합니다. |
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

3. **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹** 을 차례로 선택합니다.
  
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

## <a name="create-an-azure-sql-server-and-private-endpoint"></a><a name ="create-a-private-endpoint"></a>Azure SQL 서버 및 프라이빗 엔드포인트 만들기

이 섹션에서는 Azure에서 SQL 서버를 만듭니다. 

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **SQL 데이터베이스** 를 선택합니다.

1. **SQL 데이터베이스 만들기** 의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **CreateSQLEndpointTutorial** 을 선택합니다. 이전 섹션에서 이 리소스 그룹을 만들었습니다.|
    | **데이터베이스 세부 정보** |  |
    | 데이터베이스 이름  | **mysqldatabase** 를 입력합니다. 이 이름을 사용하는 경우 고유한 이름을 만듭니다. |
    | 서버 | **새로 만들기** 를 선택합니다. |

6. **새 서버** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 서버 이름  | **mysqlserver** 를 입력합니다. 이 이름을 사용하는 경우 고유한 이름을 만듭니다.|
    | 서버 관리자 로그인 | 원하는 관리자 이름을 입력합니다. |
    | 암호 | 선택한 암호를 입력합니다. 암호는 8자 이상이어야 하며 정의된 요구 사항을 충족해야 합니다. |
    | 위치 | **미국 동부** 를 선택합니다. |
    
7. **확인** 을 선택합니다.

8. **네트워킹** 탭을 선택하거나 **다음: 네트워킹** 단추를 선택합니다.

9. **네트워킹** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **네트워크 연결** | |
    | 연결 방법 | **프라이빗 엔드포인트** 를 선택합니다. |
   
10. **프라이빗 엔드포인트** 에서 **+ 프라이빗 엔드포인트 추가** 를 선택합니다.

11. **프라이빗 엔드포인트 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **CreateSQLEndpointTutorial** 을 선택합니다. |
    | 위치 | **미국 동부** 를 선택합니다. |
    | Name | **myPrivateSQLendpoint** 를 입력합니다. |
    | 대상 하위 리소스 | **SQLServer** 를 선택합니다. |
    | **네트워킹** |  |
    | 가상 네트워크 | **myVNet** 을 선택합니다. |
    | 서브넷 | **mySubnet** 을 선택합니다. |
    | **프라이빗 DNS 통합** | |
    | 프라이빗 DNS 영역과 통합 | **예**(기본값)를 그대로 둡니다. |
    | 프라이빗 DNS 영역 | **(새 항목) privatelink.database.windows.net**(기본값)을 그대로 둡니다. |

12. **확인** 을 선택합니다. 

13. **검토 + 만들기** 를 선택합니다.

14. **만들기** 를 선택합니다.

## <a name="test-connectivity-to-private-endpoint"></a>프라이빗 엔드포인트에 연결 테스트

이 섹션에서는 이전 단계에서 만든 가상 머신을 사용하여 프라이빗 엔드포인트에서 SQL 서버에 연결합니다.

1. 왼쪽 탐색 창에서 **리소스 그룹** 을 선택합니다.

2. **CreateSQLEndpointTutorial** 을 선택합니다.

3. **myVM** 을 선택합니다.

4. **myVM** 에 대한 개요 페이지에서 **연결** 을 선택한 다음, **Bastion** 을 선택합니다.

5. 파란색 **Bastion 사용** 단추를 선택합니다.

6. 가상 머신 만들기에서 입력한 사용자 이름과 암호를 입력합니다.

7. 연결한 후 서버에서 Windows PowerShell을 엽니다.

8. `nslookup <sqlserver-name>.database.windows.net`를 입력합니다. **\<sqlserver-name>** 을 이전 단계에서 만든 SQL 서버의 이름으로 바꿉니다.  아래 표시된 것과 유사한 메시지가 표시됩니다.

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.1.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    SQL 서버 이름에 대해 **10.1.0.5** 의 개인 IP 주소가 반환됩니다.  이 주소는 이전에 만든 가상 네트워크의 서브넷에 있습니다.


9. [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017&preserve-view=true)를 **myVM** 에 설치합니다.

10. **SQL Server Management Studio** 를 엽니다.

4. **서버에 연결** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 서버 유형 | **데이터베이스 엔진** 을 선택합니다.|
    | 서버 이름 | **\<sqlserver-name>.database.windows.net** 을 입력합니다. |
    | 인증 | **SQL Server 인증** 을 선택합니다. |
    | 사용자 이름 | 서버를 만드는 동안 입력한 사용자 이름을 입력합니다. |
    | 암호 | 서버를 만드는 동안 입력한 암호를 입력합니다. |
    | 암호 기억 | **예** 를 선택합니다. |

1. **연결** 을 선택합니다.
2. 왼쪽 메뉴에서 데이터베이스를 찾아봅니다.
3. (선택 사항) **mysqldatabase** 에서 정보를 만들거나 쿼리합니다.
4. **myVM** 에 대한 원격 데스크톱 연결을 닫습니다. 

## <a name="clean-up-resources"></a>리소스 정리 
프라이빗 엔드포인트, SQL Server 및 VM을 다 사용했으면 리소스 그룹과 리소스 그룹에 포함된 모든 리소스를 삭제합니다. 
1. 포털 위쪽의 **검색** 상자에서 **CreateSQLEndpointTutorial** 을 입력하고, 검색 결과에서 **CreateSQLEndpointTutorial** 을 선택합니다. 
2. **리소스 그룹 삭제** 를 선택합니다. 
3. **리소스 그룹 이름 입력** 에 대해 CreateSQLEndpointTutorial을 입력하고, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 만들었습니다.

* 가상 네트워크 및 베스천 호스트
* 가상 머신
* 프라이빗 엔드포인트를 사용하는 Azure SQL 서버

가상 머신을 사용하여 프라이빗 엔드포인트에서 SQL 서버에 대한 연결을 안전하게 테스트했습니다.

Private Link 서비스를 만드는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [Private Link 서비스 만들기](create-private-link-service-portal.md)
