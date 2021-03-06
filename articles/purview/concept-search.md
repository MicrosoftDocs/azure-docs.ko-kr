---
title: Azure 부서의 범위의 검색 기능 이해 (미리 보기)
description: 이 문서에서는 Azure 부서의 범위에서 검색 기능을 통해 데이터 검색을 사용 하도록 설정 하는 방법을 설명 합니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: af8ec9e0aac38240c7da92edd614892ff65712e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553965"
---
# <a name="understand-search-features-in-azure-purview"></a>Azure 부서의 범위의 검색 기능 이해

이 문서에서는 Azure 부서의 범위의 검색 환경에 대 한 개요를 제공 합니다. 검색은 데이터 검색 및 데이터를 조직에서 사용 하는 거 버 넌 스 환경을 지 원하는 부서의 범위의 핵심 플랫폼 기능입니다.

## <a name="search"></a>검색

부서의 범위 검색 환경은 관리 검색 인덱스에 의해 구동 됩니다. 부서의 범위를 사용 하 여 데이터 원본을 등록 한 후에는 검색 서비스에서 해당 메타 데이터를 인덱싱하여 검색을 쉽게 수행할 수 있습니다. 인덱스는 검색 관련성 기능을 제공 하 고 수백만 개의 메타 데이터 자산을 쿼리하여 검색 요청을 완료 합니다. 검색을 사용 하면 데이터를 검색, 이해 및 사용 하 여 데이터를 최대한 활용 하는 데 도움이 됩니다.

부서의 범위의 검색 환경은 3 단계 프로세스입니다.

1. 검색 상자는 최근 사용한 키워드 및 자산을 포함 하는 기록을 표시 합니다.
1. 키 입력을 입력 하기 시작 하면 검색에서 일치 하는 키워드와 자산을 제안 합니다. 
1. 검색 결과 페이지에는 입력 한 키워드와 일치 하는 자산이 표시 됩니다.

## <a name="reduce-the-time-to-discover-data"></a>데이터 검색 시간 단축

데이터 검색은 데이터 소비자를 위한 데이터 분석 또는 데이터 거 버 넌 스 작업에 대 한 첫 번째 단계입니다. 데이터 검색은 원하는 데이터의 위치를 알 수 없기 때문에 시간이 많이 걸릴 수 있습니다. 데이터를 찾은 후에도 데이터를 신뢰 하 고이에 대 한 종속성을 얻으려 하거나 여부에 대 한 정보를 가져올 수 있습니다. 

Azure 부서의 범위에서 검색의 목표는 중요 한 데이터를 신속 하 게 찾을 수 있도록 제스처를 제공 하 여 데이터 검색 프로세스의 속도를 높이는 것입니다.

## <a name="recent-search-and-suggestions"></a>최근 검색 및 제안

여러 프로젝트에서 동시에 작업 하는 경우가 많습니다. 이전 프로젝트를 쉽게 다시 시작할 수 있도록 부서의 범위 search는 최근 검색 키워드 및 제안을 볼 수 있는 기능을 제공 합니다. 검색 상자 드롭다운에서 **모두 보기** 를 선택 하 여 최근 검색 기록을 관리할 수도 있습니다.

## <a name="filters"></a>필터

필터 ( *패싯이* 라고도 함)는 검색을 보완 하도록 디자인 되었습니다. 검색 결과 페이지에 필터가 표시 됩니다. 검색 결과 페이지의 필터에는 분류, 민감도 레이블, 데이터 원본 및 소유자가 포함 됩니다. 사용자는 필터에서 특정 값을 선택 하 여 일치 하는 데이터 자산만 확인 하 고 일치 하는 자산으로 검색 결과를 제한할 수 있습니다.

## <a name="hit-highlighting"></a>적중 항목 강조 표시

검색 결과 페이지에서 일치 하는 키워드는 검색에서 데이터 자산이 반환 된 이유를 쉽게 식별할 수 있도록 강조 표시 됩니다. 키워드 일치는 데이터 자산 이름, 설명, 소유자 등의 여러 필드에서 발생할 수 있습니다.

적중 항목 강조 표시를 사용 하는 경우에도 데이터 자산이 검색에 포함 되는 이유는 명확 하지 않을 수 있습니다. 모든 속성은 기본적으로 검색 됩니다. 따라서 열 수준 속성의 일치 때문에 데이터 자산이 반환 될 수 있습니다. 여러 사용자가 고유한 분류 및 설명으로 데이터 자산에 주석을 달 수 있으므로 모든 메타 데이터는 검색 결과 목록에 표시 되지 않습니다.

## <a name="sort"></a>정렬

사용자는 검색 결과를 정렬 하는 두 가지 옵션을 사용할 수 있습니다. 자산 이름 또는 기본 검색 관련성을 기준으로 정렬할 수 있습니다.

## <a name="search-relevance"></a>검색 관련성

관련성은 검색 결과 페이지에서 기본 정렬 순서입니다. 검색 관련성은 검색 키워드 (일부 또는 모두)를 포함 하는 문서를 찾습니다. 검색 키워드의 많은 인스턴스를 포함 하는 자산은 더 높은 순위를 갖습니다. 또한 사용자 지정 점수 매기기 메커니즘이 지속적으로 조정 되어 검색 관련성이 향상 됩니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal에서 Azure Purview 계정 만들기](create-catalog-portal.md)
* [빠른 시작: Azure PowerShell/Azure CLI를 사용하여 Azure Purview 계정 만들기](create-catalog-powershell.md)
* [빠른 시작: 부서의 범위 Studio 사용](use-purview-studio.md)
