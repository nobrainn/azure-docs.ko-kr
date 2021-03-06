---
title: 포함 파일
description: 포함 파일
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.openlocfilehash: caa942c4bbff93f51ed311e01ecdb77bc938ac2c
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133870"
---
## <a name="create-a-personalizer-azure-resource"></a>Personalizer Azure 리소스 만들기

로컬 머신에서 [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 또는 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)를 사용하여 Personalizer용 리소스를 만듭니다. 

리소스에서 키를 가져온 후에는 다음과 같은 두 가지 [환경 변수](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)를 만듭니다.

* 리소스 키에 대한 `PERSONALIZER_RESOURCE_KEY`
* 리소스 엔드포인트에 대한 `PERSONALIZER_RESOURCE_ENDPOINT`

Azure Portal의 **빠른 시작** 페이지에서 키와 엔드포인트 값을 모두 확인할 수 있습니다.
