---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673345"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Azure Storage 연결 문자열 검색

이전에는 함수 앱에서 사용할 Azure Storage 계정을 만들었습니다. 이 계정의 연결 문자열은 Azure의 앱 설정에 안전하게 저장됩니다. 이 설정을 *local.settings.json* 파일에 다운로드하면 함수를 로컬로 실행할 때 동일한 계정의 Storage 큐에 해당 연결 쓰기를 사용할 수 있습니다. 

1. 프로젝트의 루트에서 다음 명령을 실행합니다. 여기서 `<app_name>`을 이전 빠른 시작의 함수 앱 이름으로 바꿉니다. 이 명령은 파일의 기존 값을 덮어씁니다.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. *local.settings.json*을 열고, Storage 계정 연결 문자열인 `AzureWebJobsStorage`라는 값을 찾습니다. 이 문서의 다른 섹션에서 `AzureWebJobsStorage` 이름과 연결 문자열을 사용합니다.

> [!IMPORTANT]
> *local.settings.json*에는 Azure에서 다운로드한 비밀이 포함되어 있으므로 항상 이 파일을 원본 제어에서 제외하세요. 로컬 함수 프로젝트를 사용하여 만든 *.gitignore* 파일은 기본적으로 해당 파일이 제외됩니다.