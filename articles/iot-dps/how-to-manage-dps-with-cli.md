---
title: IoT 확장 Azure CLI & 사용 하 여 IoT Hub Device Provisioning Service 관리
description: Azure CLI 및 IoT 확장을 사용 하 여 IoT Hub Device Provisioning Service (DPS)를 관리 하는 방법을 알아봅니다.
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0ba92279632a7283ea6ede423e808e3c7be82cff
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975162"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Azure CLI 및 IoT 확장을 사용 하 여 IoT Hub Device Provisioning Service를 관리 하는 방법

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 은 IoT Edge와 같은 Azure 리소스를 관리 하기 위한 오픈 소스 플랫폼 간 명령줄 도구입니다. Azure CLI는 Windows, Linux 및 MacOS에서 사용할 수 있습니다. Azure CLI를 사용 하면 Azure IoT Hub 리소스, 장치 프로 비전 서비스 인스턴스 및 연결 된 허브를 즉시 관리할 수 있습니다.

IoT 확장은 장치 관리 및 전체 IoT Edge 기능과 같은 기능을 Azure CLI 강화.

이 자습서에서는 먼저 Azure CLI 및 IoT 확장을 설정 하는 단계를 완료 합니다. 그런 다음 CLI 명령을 실행 하 여 기본 장치 프로 비전 서비스 작업을 수행 하는 방법을 알아봅니다. 

## <a name="installation"></a>Telepítés 

### <a name="step-1---install-python"></a>1\. lépés – A Python telepítése

[Python 2.7x vagy Python 3.x](https://www.python.org/downloads/) szükséges.

### <a name="step-2---install-the-azure-cli"></a>2 단계-Azure CLI 설치

[설치 지침](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 에 따라 환경에 Azure CLI을 설정 합니다. 최소한 Azure CLI 버전은 2.0.24 이상 이상 이어야 합니다. A verziószámot az `az –version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. Windows rendszeren a legegyszerűbb megoldás a telepítésre az [MSI](https://aka.ms/InstallAzureCliWindows) letöltése és telepítése.

### <a name="step-3---install-iot-extension"></a>3\. lépés – Az IoT-bővítmény telepítése

[Az IoT-bővítmény fontos (readme) fájlja](https://github.com/Azure/azure-iot-cli-extension) több módszert is ismertet a bővítmény telepítésére. A legegyszerűbb módszer az `az extension add --name azure-cli-iot-ext` futtatása. A telepítés után az `az extension list` paranccsal ellenőrizheti az aktuálisan telepített bővítményeket, az `az extension show --name azure-cli-iot-ext` paranccsal pedig megtekintheti az IoT-bővítmény adatait. A bővítményt az `az extension remove --name azure-cli-iot-ext` paranccsal távolíthatja el.


## <a name="basic-device-provisioning-service-operations"></a>기본 장치 프로 비전 서비스 작업
이 예제에서는 azure 계정에 로그인 하 고, Azure 리소스 그룹 (Azure 솔루션에 관련 된 리소스를 보유 하는 컨테이너)을 만들고, IoT Hub 만들고, 장치 프로 비전 서비스를 만들고, 기존 장치 프로 비전 서비스를 나열 하는 방법을 보여 줍니다. CLI 명령을 사용 하 여 연결 된 IoT hub를 만듭니다. 

Mielőtt nekikezdene, végezze el az előzőekben ismertetett telepítési lépéseket. Ha még nem rendelkezik Azure-előfizetéssel, akár most is létrehozhat [egy ingyenes fiókot](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Azure 계정에 로그인 합니다.
  
    az login

![bejelentkezés][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. eIoTHubBlogDemo에서 리소스 그룹 만들기

    az group create -l eastus -n IoTHubBlogDemo

![Erőforráscsoport létrehozása][2]


### <a name="3-create-two-device-provisioning-services"></a>3. 두 개의 장치 프로 비전 서비스 만들기

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![장치 프로 비전 서비스 만들기][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4 .이 리소스 그룹의 기존 장치 프로 비전 서비스를 모두 나열 합니다.

    az iot dps list --resource-group IoTHubBlogDemo

![장치 프로 비전 서비스 나열][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. 새로 만든 리소스 그룹 아래에 IoT Hub blogDemoHub를 만듭니다.

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![IoT Hub létrehozása][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. 장치 프로 비전 서비스에 기존 IoT Hub 하나 연결

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Hub csatolása][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az eszköz regisztrálása
> * Az eszköz elindítása
> * Az eszköz sikeres regisztrálásának ellenőrzése

A következő oktatóanyag azt mutatja be, hogyan regisztrálhat több eszközt az elosztott terhelésű hubok között. 

> [!div class="nextstepaction"]
> [Eszközök regisztrálása elosztott terhelésű IoT Hubokon](./tutorial-provision-multiple-hubs.md)
