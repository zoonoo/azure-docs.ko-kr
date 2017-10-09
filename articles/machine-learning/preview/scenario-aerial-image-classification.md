---
title: "항공 이미지 분류 | Microsoft Docs"
description: "항공 이미지 분류 실제 시나리오에 대한 지침을 제공합니다."
author: mawah
ms.author: mawah
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.date: 09/15/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 43b124fc3eb72adc5d299b218c9e16ec83d1a240
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="aerial-image-classification"></a>항공 이미지 분류

이 예제는 Azure Machine Learning Workbench를 사용해 이미지 분류 모델의 분산된 교육 및 운영을 조정하는 방법을 보여줍니다. 미리 교육된 CNTK 모델을 사용하여 이미지를 기능화하고 파생된 기능을 사용하여 분류자를 교육하도록 [Microsoft Machine Learning for Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) 패키지를 사용합니다. 그런 후 교육된 모델을 그대로 클라우드에 있는 큰 이미지 집합에 적용합니다. 이 단계는 [Azure HDInsight Spark](https://azure.microsoft.com/en-us/services/hdinsight/apache-spark/) 클러스터에서 수행되어 작업자 노드를 추가 또는 제거하여 교육 및 운영 속도를 변경할 수 있게 해 줍니다.

우리가 보여준 전이 교육의 양식은 심층 신경망을 재교육하거나 미세 조정하는 것에 비해 주된 장점이 있습니다. GPU 컴퓨팅을 필요로 하지 않으며, 본질적으로 빠르고 임의로 확장할 수 있으며, 매개 변수도 적게 사용합니다. 따라서 이 메서드는 사용자 지정 사용 사례인 경우 흔히 사용되듯이 교육 샘플이 거의 없는 경우에 매우 적합합니다. 많은 사용자가 훨씬 큰 비용을 들여 처음부터 교육되는 심층 신경망을 피할 수 있어 전이 교육이 성능이 뛰어난 모델을 생성한다고 보고합니다.

이 예에 사용된 Spark 클러스터는 40개의 작업자 노드가 있으며 운영하는 데 시간당 40불의 비용이 듭니다. 이 연습을 완료하려면 약 2시간 가량 걸립니다. 연습을 마치면 정리 지침에 따라 만든 리소스를 제거하여 더 이상 비용이 발생하지 않게 합니다.

## <a name="link-to-the-gallery-github-repository"></a>갤러리 GitHub 리포지토리에 연결

이 실제 시나리오에 대한 공용 GitHub 리포지토리에는 이 예제에 필요한 코드 샘플을 포함한 모든 자료가 들어 있습니다.

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>사용 사례 설명

이 시나리오에서는 224미터 x 224미터 평면도 항공 이미지에 보이는 토지 유형을 분류할 기계 교육 모델을 교육합니다. 토지 사용 분류 모델은 정기적으로 수집된 항공 이미지를 사용하여 도시화, 삼림 벌채, 습지 유실 및 기타 주요 환경적 경향을 추적하는 데 사용할 수 있습니다. 미국의 이미지를 기반으로 교육 및 유효성 검사 이미지 집합을 준비했습니다. National Agriculture Imagery Program and land는 미국에서 게시하는 라벨을 사용합니다. National Land Cover Database. 각 토지의 이미지가 클래스를 사용하는 예제는 다음과 같습니다.

![각 토지가 라벨을 사용하는 예제](media/scenario-aerial-image-classification/example-labels.PNG)

분류자 모델을 교육하고 유효성 검사를 한 후, 우리는 이러한 모델이 도시 개발에서 추세를 연구하는 데 어떻게 사용될 수 있는지 보여주기 위해 Microsoft의 뉴 잉글랜드 연구 및 개발(NERD) 센터가 위치한 메사추세츠 미들섹스 카운티의 항공 사진에 적용할 것입니다.

전이 학습을 사용하는 이미지 분류자를 생성하기 위해 데이터 과학자는 흔히 다양한 메서드로 여러 모델을 생성한 후 가장 성능이 좋은 모델을 선택합니다. 데이터 과학자는 Azure Machine Learning Workbench를 활용하여 여러 다른 컴퓨팅 환경에 걸쳐 교육을 조정하고, 여러 모델의 성능을 추적 및 비교하여, 선택한 모델을 클라우드에 있는 큰 데이터 집합에 적용할 수 있습니다.

## <a name="scenario-structure"></a>시나리오 구조

이 예제에서는 이미지 데이터와 미리 교육된 모델이 Azure 저장소 계정에 들어 있습니다. Azure HDInsight Spark 클러스터는 MMLSpark를 사용하여 이러한 파일을 읽고 이미지 분류 모델과 생성합니다. 교육된 모델 및 예측은 저장소 계정에 기록되어, 로컬에서 실행되는 Jupyter 노트북으로 분석하고 시각화할 수 있습니다. Azure Machine Learning Workbench는 스크립트를 Spark 클러스터에 원격 실행하는 것을 조정합니다. 또한 사용자가 가장 성능이 좋은 모델을 선택할 수 있도록 다양한 방법을 사용하여 교육한 여러 모델에 대한 정확도 메트릭을 추적합니다.

![항공 이미지 분류 실제 시나리오에 대한 계통도](media/scenario-aerial-image-classification/scenario-schematic.PNG)

[단계별 지침](https://github.com/MicrosoftDocs/azure-docs-pr/tree/release-ignite-aml-v2/articles/machine-learning/)은 데이터 전송 및 종속성 설치를 포함하여 Azure 저장소 계정 및 Spark 클러스터 생성과 준비하는 과정을 안내하는 것으로 시작합니다. 그런 다음 교육 작업을 시작하고 결과 모델의 성능을 비교하는 방법을 설명합니다. 마지막으로, 선택한 모델을 Spark 클러스터의 큰 이미지 집합에 적용하여 로컬에서 예측 결과 분석하는 방법을 보여 줍니다.


## <a name="set-up-the-execution-environment"></a>실행 환경 설정

다음 지침에서는 이 예제에 대한 실행 환경을 설정하는 과정을 안내합니다.

### <a name="prerequisites"></a>필수 조건
- [Azure 계정](https://azure.microsoft.com/en-us/free/)(무료 평가판 사용 가능)
    - 이 샘플은 40 작업자 노드(총 168 코어)로 HDInsight Spark 클러스터를 만듭니다. 사용자 계정에 사용 가능한 충분한 코어가 있는지 Azure Portal에서 구독에 대한 "사용 + 할당량" 탭을 검토하여 확인합니다.
    - 사용할 수 있는 코어가 적다면 HDInsight 클러스터 템플릿을 수정하여 프로비전된 사용자 수를 줄일 수 있습니다. 이에 대한 지침은 "HDInsight Spark 클러스터 만들기" 섹션 아래에 있습니다.
- [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md)
    - [빠른 시작 설치 및 만들기](quickstart-installation.md)에 따라 Azure Machine Learning Workbench를 설치하고 Experimentation and Model Management Accounts(실험 및 모델 관리 계정)을 만듭니다.
- [AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy), Azure 저장소 계정 간의 파일 전송을 조정하기 위한 유틸리티
    - AzCopy 실행 파일이 있는 폴더가 시스템의 PATH 환경 변수에 있는지 확인합니다. (환경 변수를 수정하는 방법에 대한 지침은 [여기](https://support.microsoft.com/en-us/help/310519/how-to-manage-environment-variables-in-windows-xp)에 있습니다). 이 예제는 Windows 10 PC에서 테스트 되었습니다; Azure 데이터 과학 가상 컴퓨터를 포함하여 모든 Windows 컴퓨터에서 실행할 수 있어야 합니다. 이 예제를 macOS에서 실행할 경우 약간의 수정이 필요할 수도 있습니다.(예: 파일 경로 변경)

### <a name="set-up-azure-resources"></a>Azure 리소스 설정

이 예제는 관련 파일을 호스트하기 위해 HDInsight Spark 클러스터 및 Azure 저장소 계정이 필요합니다. 새 Azure 리소스 그룹에 이러한 리소스를 만들려면 다음 지침을 따릅니다.

#### <a name="create-a-new-workbench-project"></a>새 워크벤치 프로젝트 만들기

이 예제를 템플릿으로 사용하여 새 프로젝트를 만듭니다.
1.  Azure Machine Learning Workbench 열기
2.  **프로젝트** 페이지에서 **+** 부호를 클릭하고 **새 프로젝트**를 선택합니다.
3.  **새 프로젝트 만들기** 창에서 새 프로젝트에 대한 정보를 입력합니다.
4.  **Search Project Templates** 검색 상자에서 "Aerial Image Classification(항공 이미지 분류)"을 입력하고 템플릿을 선택합니다.
5.  **만들기**를 클릭합니다.
 
#### <a name="create-the-resource-group"></a>리소스 그룹을 만듭니다.

1. Azure Machine Learning Workbench 프로젝트에서 File -> Open Command Prompt를 클릭하여 명령줄 인터페이스(CLI)를 엽니다.
1. 명령줄 인터페이스에서 다음 명령을 실행하여 Azure 계정에 로그인합니다.

    ````
    az login
    ```

    You are asked to visit a URL and type in a provided temporary code; the website requests your Azure account credentials.
    
1. When login is complete, return to the CLI and run the following command to determine which Azure subscriptions are available to your Azure account:

    ```
    az account list
    ```

    This command lists all subscriptions associated with your Azure account. Find the ID of the subscription you would like to use. Write the subscription ID where indicated in the following command, then set the active subscription by executing the command:

    ```
    az account set --subscription [subscription ID]
    ```

1. The Azure resources created in this example are stored together in an Azure resource group. Choose a unique resource group name and write it where indicated, then execute both commands to create the Azure resource group:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### Create the storage account

We now create the storage account that hosts project files that must be accessed by the HDInsight Spark.

1. Choose a unique storage account name and write it where indicated in the following `set` command, then create an Azure storage account by executing both commands:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Issue the following command to list the storage account keys:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Record the value of `key1` as the storage key in the following command, then run the command to store the value.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. In your favorite text editor, load the `settings.cfg` file from the Azure Machine Learning Workbench project's "Code" subdirectory, and insert the storage account name and key as indicated. Save and close the `settings.cfg` file.
1. If you have not already done so, download and install the [AzCopy](http://aka.ms/downloadazcopy) utility. Ensure that the AzCopy executable is on your system path by typing "AzCopy" and pressing Enter to show its documentation.
1. Issue the following commands to copy all of the sample data, pretrained models, and model training scripts to the appropriate locations in your storage account:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Expect file transfer to take up to 20 minutes. While you wait, you can proceed to the following section. You may need to open another Command Line Interface through Workbench and redefine the temporary variables there.

#### Create the HDInsight Spark cluster

Our recommended method to create an HDInsight cluster uses the HDInsight Spark cluster Resource Manager template included in the "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" subfolder of this project.

1. The HDInsight Spark cluster template is the "template.json" file under the "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" subfolder of this project. By default, the template creates a Spark cluster with 40 worker nodes. If you must adjust that number, open the template in your favorite text editor and replace all instances of "40" with the worker node number of your choice.
    - You may encounter out-of-memory errors if the number of worker nodes you choose is small. To combat memory errors, you may run the training and operationalization scripts on a subset of the available data as described later in this document.
2. Choose a unique name and password for the HDInsight cluster and write them where indicated in the following command. Then create the cluster by issuing the following command:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

Your cluster's deployment may take up to 30 minutes (including provisioning and script action execution).

### Prepare the Azure Machine Learning Workbench execution environment

#### Register the HDInsight cluster as an Azure Machine Learning Workbench compute target

Once HDInsight cluster creation is complete, register the cluster as a compute target for your project as follows:

1.  Issue the following command from the Azure Machine Learning Command Line Interface:

    ```
    az ml computetarget attach --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD% -t cluster
    ```

    This command adds two files, `myhdi.runconfig` and `myhdi.compute`, to your project's `aml_config` folder.

1. Open the `myhdi.compute` file in your favorite text editor. Modify the `yarnDeployMode: cluster` line to read `yarnDeployMode: client`, then save and close the file.
1. Run the following command to prepare your environment for use:
   ```
   az ml experiment prepare -c myhdi
   ```

#### Install local dependencies

Open a CLI from Azure Machine Learning Workbench and install dependencies needed for local execution by issuing the following command:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn
```

## Data acquisition and understanding

This scenario uses publicly available aerial imagery data from the [National Agriculture Imagery Program](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) at 1-meter resolution. We have generated sets of 224 pixel x 224 pixel PNG files cropped from the original NAIP data and sorted according to land use labels from the [National Land Cover Database](https://www.mrlc.gov/nlcd2011.php). A sample image with label "Developed" is shown at full size:

![A sample tile of developed land](media/scenario-aerial-image-classification/sample-tile-developed.png)

Class-balanced sets of ~44k and 11k images are used for model training and validation, respectively. We demonstrate model deployment on a ~67k image set tiling Middlesex County, MA -- home of Microsoft's New England Research and Development (NERD) center. For more information on how these image sets were constructed, see the [Embarrassingly Parallel Image Classification git repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Location of Middlesex County, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

During setup, the aerial image sets used in this example were transferred to the storage account that you created. The training, validation, and operationalization images are all 224 pixel x 224 pixel PNG files at a resolution of one pixel per square meter. The training and validation images have been organized into subfolders based on their land use label. (The land use labels of the operationalization images are unknown and in many cases ambiguous; some of these images contain multiple land types.) For more information on how these image sets were constructed, see the [Embarrassingly Parallel Image Classification git repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

To view example images in your Azure storage account (optional):
1. Log in to the [Azure portal](https://portal.azure.com).
1. Search for the name of your storage account in the search bar along the top of your screen. Click on your storage account in the search results.
2. Click on the "Blobs" link in the storage account's main pane.
3. Click on the container named "train." You should see a list of directories named according to land use.
4. Click on any of these directories to load the list of images it contains.
5. Click on any image and download it to view the image.
6. If desired, click on the containers named "test" and "middlesexma2016" to view their contents as well.

## Modeling

### Training models with MMLSpark
The `run_mmlspark.py` script in the "Code\02_Modeling" subfolder of the Workbench project is used to train an [MMLSpark](https://github.com/Azure/mmlspark) model for image classification. The script first featurizes the training set images using an image classifier DNN pretrained on the ImageNet dataset (either AlexNet or an 18-layer ResNet). The script then uses the featurized images to train an MMLSpark model (either a random forest or a logistic regression model) to classify the images. The test image set is then featurized and scored with the trained model. The accuracy of the model's predictions on the test set is calculated and logged to Azure Machine Learning Workbench's run history feature. Finally, the trained MMLSpark model and its predictions on the test set are saved to blob storage.

Select a unique output model name for your trained model, a pretrained model type, and an MMLSpark model type. Write your selections where indicated in the following command template, then begin retraining by executing the command from an Azure ML Command Line Interface:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

An additional `--sample_frac` parameter can be used to train and test the model with a subset of available data. Using a small sample fraction decreases runtime and memory requirements at the expense of trained model accuracy. For more information on this and other parameters, run `python Code\02_Modeling\run_mmlspark.py -h`.

Users are encouraged to run this script several times with different input parameters. The performance of the resulting models can then be compared in Azure Machine Learning Workbench's Run History feature.

### Comparing model performance using the Workbench Run History feature

After you have executed two or more training runs of either type, navigate to the Run History feature in Workbench by clicking the clock icon along the left-hand menu bar. Select `run_mmlspark.py` from the list of scripts at left. A pane loads comparing the test set accuracy for all runs. To see more detail, scroll down and click on the name of an individual run.

## Deployment

To apply one of your trained models to aerial images tiling Middlesex County, MA using remote execution on HDInsight, insert your desired model's name into the following command and execute it:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

An additional `--sample_frac` parameter can be used to operationalize the model with a subset of available data. Using a small sample fraction decreases runtime and memory requirements at the expense of prediction completeness. For more information on this and other parameters, run `python Code\03_Deployment\batch_score_spark -h`.

This script writes the model's predictions to your storage account. The predictions can be examined as described in the next section.

## Visualization

The "Model prediction analysis" Jupyter notebook in the "Code\04_Result_Analysis" subfolder of the Workbench project visualizes a model's predictions. Load and run the notebook as follows:
1. Open the project in Workbench and click on the folder ("Files") icon along the left-hand menu to load the directory listing.
2. Navigate to the "Code\04_Result_Analysis" subfolder and click on the notebook named "Model prediction analysis." A preview rendering of the notebook should be displayed.
3. Click "Start Notebook Server" to load the notebook.
4. In the first cell, enter the name of the model whose results you would like to analyze where indicated.
5. Click on "Cell -> Run All" to execute all cells in the notebook.
6. Read along with the notebook to learn more about the analyses and visualizations it presents.

## Cleanup
When you have completed the example, we recommend that you delete all of the resources you have created by executing the following command from the Azure Command Line Interface:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## References

- [The Embarrassingly Parallel Image Classification repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Describes dataset construction from freely available imagery and labels
- [MMLSpark](https://github.com/Azure/mmlspark) GitHub repository
   - Contains additional examples of model training and evaluation with MMLSpark

## Conclusions

Azure Machine Learning Workbench helps data scientists easily deploy their code on remote compute targets. In this example, local code was deployed for remote execution on an HDInsight cluster. Azure Machine Learning Workbench's run history feature tracked the performance of multiple models and helped us identify the most accurate model. Workbench's Jupyter notebooks feature helped visualize our models' predictions in an interactive, graphical environment.

## Next steps
To dive deeper into this example:
- In Azure Machine Learning Workbench's Run History feature, click the gear symbols to select which graphs and metrics are displayed.
- Examine the sample scripts for statements calling the `run_logger`. Check that you understand how each metric is being recorded.
- Examine the sample scripts for statements calling the `blob_service`. Check that you understand how trained models and predictions are stored and retrieved from the cloud.
- Explore the contents of the containers created in your blob storage account. Ensure that you understand which script or command is responsible for creating each group of files.
- Modify the training script to train a different MMLSpark model type or to change the model hyperparameters. Use the run history feature to determine whether your changes increased or decreased the model's accuracy.

