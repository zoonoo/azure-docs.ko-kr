<properties
	pageTitle="OS 플랫폼 및 하드웨어 호환성 | Microsoft Azure"
	description="OS 플랫폼 및 장치 하드웨어와 IoT 장치 SDK 호환성을 요약합니다."
	services="iot-hub"
	documentationCenter=""
	authors="hegate"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/03/2016"
     ms.author="hegate"/>

# OS 플랫폼 및 장치 SDK와의 하드웨어 호환성

이 문서에서는 [IoT용 Microsoft Azure 인증 프로그램](#microsoft-azure-certified-for-iot)에 포함되어 있는 특정 장치 구성뿐만 아니라 다른 OS 플랫폼의 SDK 호환성에 대해 설명합니다. 장치가 이미 있는 경우 호환성에 대한 장치 특정 정보를 찾으려면 프로그램에서 포함된 장치 목록을 확인하세요. 어떤 장치를 사용할지 확인하지 않은 경우 [OS 플랫폼 및 라이브러리](#os-platforms) 호환 섹션을 확인하세요.


## OS 플랫폼

Azure IoT 라이브러리는 다음 운영 체제 플랫폼에서 테스트되었습니다.


|Linux/Unix OS 플랫폼 | 버전|
|:---------------|:------------:|
|Debian Linux| 7\.5|
|Fedora Linux|20|
|Raspbian Linux| 3\.18 |
|Ubuntu Linux| 14\.04 |
|Yocto Linux|2\.1 |

|Windows OS 플랫폼 | 버전|
|:---------------|:------------:|
|Windows 데스크톱| 7,8,10 |
|Windows IoT 코어| 10 |
|Windows Server| 2012 R2|

|기타 플랫폼 | 버전|
|:---------------|:------------:|
|mbed | 2\.0 |
|TI-RTOS | 2\.x |



## C 라이브러리

[C용 Microsoft Azure IoT 장치 SDK](https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md)는 다음 구성에서 테스트되었습니다.

|OS 플랫폼| 버전|프로토콜|
|:---------|:----------:|:----------:|
|Debian Linux| 7\.5 | HTTPS, AMQP, MQTT |
|Fedora Linux| 20 | HTTPS, AMQP, MQTT |
|mbed OS| 2\.0 | HTTPS, AMQP |
|TI-RTOS| 2\.x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS, AMQP, MQTT |
|Windows 데스크톱| 7,8,10 | HTTPS, AMPQ, MQTT |
|Yocto Linux|2\.1 | HTTPS, AMQP|



## Node.js 라이브러리

[Node.js용 Microsoft Azure IoT 장치 SDK](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md)는 다음 구성에서 테스트되었습니다.


|런타임| 버전|프로토콜|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS|



## Java 라이브러리

[Java용 Microsoft Azure IoT 장치 SDK](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md)는 다음 구성에서 테스트되었습니다.

|런타임| 버전|프로토콜|
|:---------|:----------:|----|
|Java SE(Windows)| 1\.7 | HTTPS, AMQP |
|Java SE(Linux)| 1\.8 | HTTPS, AMQP|

Java용 Microsoft Azure IoT 서비스 SDK는 다음 구성에서 테스트되었습니다.

|런타임| 버전|프로토콜|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS, AMQP |


## CSharp

[.NET용 Microsoft Azure IoT 장치 SDK](https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md)는 다음 구성에서 테스트되었습니다.

|OS 플랫폼| 버전|프로토콜|
|:---------|:----------:|:----------:|
|Windows 데스크톱| 7,8,10 | HTTPS, AMPQ|
|Windows IoT 코어|10 | HTTPS|

관리된 에이전트 코드는 Microsoft .NET Framework 4.5가 필요함


## IoT용으로 인증된 Microsoft Azure

**IoT용으로 인증된 Microsoft Azure**는 광범위한 IoT 에코 시스템과 Microsoft Azure를 연결하여 개발자와 설계자는 호환성 시나리오를 이해할 수 있습니다. 특히 OS/장치 조합의 신뢰할 수 잇는 목록을 제공하여 개념 증명 또는 파일럿 단계에서든 IoT 프로젝트를 빠르게 시작할 수 있도록 합니다. 인증된 장치 및 운영 체제 조합을 사용하여 장치가 Azure IoT Suite 및 Azure IoT Hub와 호환하는데 필요한 적은 작업과 사용자 지정을 통해 IoT 프로젝트를 빠르게 시작할 수 있습니다.

## IoT 장치에 대한 인증

**IoT 장치에 대한 인증**은 Azure IoT SDK와의 호환성이 테스트되었으며 IoT 응용 프로그램에서 사용할 준비가 되었습니다. 특히 운영 체제 플랫폼 및 코드 언어에 따라 호환성을 식별합니다.

#### 장치 목록

각 장치는 장치 제조업체에서 선택한 OS 및 언어에서 SDK와 함께 작동하도록 인증되었습니다. 예를 들어 BeagleBone Black은 C, Javascript 및 Java 언어를 사용하여 Debian에서 작동합니다. 즉 개발자는 특정 장치에서 해당 언어 및 OS 조합 중에서 응용 프로그램을 개발할 수 있습니다.

|장치| 테스트된 OS |언어|
|:---------|:----------|:----------|
|[AAEON ACP-1104](http://www.aaeon.com/en/p/infotainment-multi-touch-panel-solutions-1104/) |Windows 10 | C#|
|[AAEON GENE-BT05](http://www.aaeon.com/en/p/3-and-half-inches-subcompact-boards-gene-bt05/) |Windows 10 | C#|
|[AAEON PICO-BT01](http://www.aaeon.com/en/p/pico-itx-boards-pico-bt01) |Windows 10 | C#|
|[AAEON UP](http://www.up-board.org/) |Windows 10 | C#|
|[Acme Systems Arietta G25](http://www.acmesystems.it/arietta) |Debian | C|
|[ADLINK MXE 202i](http://www.adlinktech.com/PD/web/PD_detail.php?pid=1589) |Wind River | JavaScript|
|[ADLINK MXE-5400](http://www.adlinktech.com/PD/web/PD_detail.php?pid=1318) |Windows 10 | C#|
|[Advantech Co., ARK-2121L](http://www.advantech.com/products/ark-2000_series_embedded_box_pcs/ark-2121l/mod_dd092808-0832-44bc-b38a-945eb7e016bd) |Windows 10 | C#|
|[Advantech Co., ARK-1123C](http://www.advantech.com/products/92d96fda-cdd3-409d-aae5-2e516c0f1b01/ark-1123c/mod_0b91165c-aa8c-485d-8d25-fde6f88f4873) |Windows 10 | C#|
|[Advantech Co., LTD UNO-1372G](http://www.advantech.com/products/gf-bvl2/uno-1372g/mod_8e63b3c9-b606-4725-a1af-94fccb98bb1a) |[Windows 10 | C#|
|[Advantech Co., TREK-674](http://www.advantech.com/products/1-2jsj5t/trek-674/mod_88a737dd-819b-4c8e-8f2e-2bb75b04619b) |Windows 10 | C#|
|[Advantech Co., UTX-3115](http://www.advantech.com/products/bda911fe-28bc-4171-aed3-67f76f6a12c8/utx-3115/mod_fa00d5cd-7d2b-430b-8983-c232bfb9f315) |Windows 10 | C#|
|[Arduino MKR1000](https://www.arduino.cc/en/Main/ArduinoMKR1000) |Arduino IDE | C|
|[Arduino Zero](https://www.arduino.cc/en/Guide/ArduinoZero) |Arduino IDE | C|
|[Arrow DragonBoard 410c](http://partners.arrow.com/campaigns-na/qualcomm/dragonboard-410c/) |Windows 10 IoT 코어 | C#|
|[Axiomtek ICO300](http://www.axiomtek.com/Default.aspx?MenuId=Products&FunctionId=ProductView&ItemId=1151) |Windows 10 | C#|
|[BeagleBone Black](http://beagleboard.org/black) | Debian | C, Javascript, Java|
|[BeagleBone Green](http://beagleboard.org/green) |Debian | C, Javascript, Java|
|[ComponentSoft RFID Tunnel](http://www.componentsoft.com/) |Windows 10 | C#|
|[Dell Edge Gateway 5000 시리즈](http://www.dell.com/IoTgateway) |Ubuntu | Java|
|[e-con Systems Almach](http://www.e-consystems.com/DM3730-development-board.asp) |Linux Yocto | C|
|[e-con Systems Ankaa](http://www.e-consystems.com/iMX6-development-board.asp) |Ubuntu | C|
|[embedded systems LogicMachine 시리즈](http://openrb.com/products/) |사용자 지정 Linux | C|
|[Freescale FRDM K64](http://www.freescale.com/products/arm-processors/kinetis-cortex-m/k-series/k6x-ethernet-mcus/freescale-freedom-development-platform-for-kinetis-k64-k63-and-k24-mcus:FRDM-K64F) |mbed 2.0 | C|
|[HPE Edgeline EL10](http://www8.hp.com/h20195/v2/GetPDF.aspx/c04884747.pdf) |Windows 10 | C#|
|[HPE Edgeline EL20](http://www8.hp.com/h20195/v2/GetPDF.aspx/c04884769.pdf) |Windows 10 | C#|
|[IEI ICECARE-10W](http://www.ieimobile.com/index.php?option=com_content&view=article&id=222&Itemid=21) |Windows 10 | C#|
|[IEI DRPC-120](http://www.ieiworld.com/product_groups/industrial/content.aspx?gid=09049552811981014603&cid=0D182494345754583862&id=0E318374091597499543#.VqW3Q_l97Dd) |Windows 10 | C#|
|[IEI IVS-100-BT](http://tw.ieiworld.com/product_groups/industrial/content.aspx?gid=09049552811981014603&cid=0F202412454715193114&id=0F202496627608256517#.VqH1hvl97Dc) |Windows 10 | C#|
|[Ilevia Eve Raspberry](http://www.ilevia.com/overview/) |Debian | C|
|[Intel Edison](http://www.intel.com/content/www/us/en/do-it-yourself/edison.html) |Yocto | C, Javascript|
|[Libelium Meshlium Xtreme](http://www.libelium.com/products/meshlium/) |Debian | Java|
|[Minnowboard Max](http://www.minnowboard.org/meet-minnowboard-max/) |Windows 7,8, 10 | C#|
|[NEXCOM NISE 50C](http://www.nexcom.com/Products/industrial-computing-solutions/industrial-fanless-computer/atom-compact/fanless-computer-nise-50c) |Windows 10 IoT 코어 | C#|
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Raspbian | C, Javascript, Java |
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Windows 10 IoT 코어| C, Javascript, C#|
|[Samsung ARTIK](http://developer.samsung.com/artik) |Fedora | C|
|[SOTEC CloudPlug](http://cloudplug.info/) |YOCTO | C|
|[TI CC3200](http://www.ti.com/product/cc3200) |TI-RTOS 2.x | C|
|[Toradex Apalis iMX6](https://www.toradex.com/computer-on-modules/apalis-arm-family/freescale-imx-6) |Linux Angstrom(Yocto) | C, Javascript, Java|
|[Toradex Apalis T30](https://www.toradex.com/computer-on-modules/apalis-arm-family/nvidia-tegra-3) |Linux Angstrom(Yocto) | C, Javascript, Java|
|[Toradex Colibri iMX6](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-imx6) |Linux Angstrom(Yocto) | C, Javascript, Java|
|[Toradex Colibri T20](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-2) |Linux Angstrom(Yocto) | Java|
|[Toradex Colibri T30](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-3) |Windows 10 IoT 코어 | C#|
|[Toradex Colibri VF61](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-vybrid-vf6xx) |Linux Angstrom(Yocto) | C, Javascript, Java|
|[Trex NGP](http://www.trex.com.tr/en/donanim_dcasngp8739_73.php) |Windows 10 | C#|
|[Trueverit V4](http://www.trueverit.com/) |사용자 지정 Linux | C|
|[USISH EDA8909](http://www.usish.com/) |Windows 10 | C#|

[이러한 장치 사용을 시작](https://azure.microsoft.com/develop/iot/get-started/)하거나 GitHub [리포지토리](https://github.com/Azure/azure-iot-sdks)를 방문하고 언어별 장치 문서를 검색하세요.

## 다음 단계

[IoT 장치에 대한 인증](http://azure.com/iotdev)을 사용하여 솔루션을 개발하는 방법에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0204_2016-->