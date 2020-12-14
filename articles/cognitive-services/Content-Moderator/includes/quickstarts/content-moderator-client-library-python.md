---
title: 内容审查器 Python 客户端库快速入门
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何开始使用适用于 Python 的 Azure 认知服务内容审查器客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/01/2020
ms.author: v-tawe
origin.date: 01/27/2020
ms.openlocfilehash: bd37b3d6d98a430aafb44aaaa694d1ed09637ebb
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105653"
---
适用于 Python 的内容审查器客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。 内容审查器是一项认知服务，用于检查文本、图像和视频中是否存在可能的冒犯性内容、有风险内容或其他令人不适的内容。 找到此类内容时，此服务会将相应的标签（标记）应用到该内容。 然后，应用会处理标记的内容，使之符合法规的要求，或者为用户维持一个理想的环境。

使用适用于 Python 的内容审查器客户端库可以：

* [审查文本](#moderate-text)
* [使用自定义字词列表](#use-a-custom-terms-list)
* [审查图像](#moderate-images)
* [使用自定义图像列表](#use-a-custom-image-list)
* [创建评审](#create-a-review)

[参考文档](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [包 (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [示例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建试用订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>设置

### <a name="create-a-content-moderator-azure-resource"></a>创建内容审查器 Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 在本地计算机上使用 [Azure 门户](/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](/cognitive-services/cognitive-services-apis-create-account-cli) 创建内容审查器的资源。 你还可以：

<!--trial key not available--> 
* 在 [Azure 门户](https://portal.azure.cn/)上查看资源

获取试用订阅或资源后，请为该密钥和终结点 URL [创建环境变量](/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)，分别名为 `CONTENT_MODERATOR_SUBSCRIPTION_KEY` 和 `CONTENT_MODERATOR_ENDPOINT`。
 
### <a name="create-a-python-script"></a>创建 Python 脚本

创建新的 Python 脚本，并在喜好的编辑器或 IDE 中打开它。 将以下 `import` 语句添加到该文件的顶部。

```python
import os.path
from pprint import pprint
import time
from io import BytesIO
from random import random
import uuid

from azure.cognitiveservices.vision.contentmoderator import ContentModeratorClient
import azure.cognitiveservices.vision.contentmoderator.models
from msrest.authentication import CognitiveServicesCredentials
```

接下来，为资源的终结点位置创建变量，并将密钥创建为环境变量。 

```python
CONTENTMODERATOR_ENDPOINT = os.environ.get("CONTENT_MODERATOR_ENDPOINT")
subscription_key = os.environ.get("CONTENT_MODERATOR_SUBSCRIPTION_KEY")
```

> [!NOTE]
> 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。

### <a name="install-the-client-library"></a>安装客户端库

可使用以下命令安装内容审查器客户端库：

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>对象模型

以下类将处理内容审查器 Python 客户端库的某些主要功能。

|名称|说明|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|所有内容审查器功能都需要此类。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。|
|[ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|此类提供用于分析成人内容、个人信息或人脸的功能。|
|[TextModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|此类提供用于在文本中分析语言、猥亵内容、错误和个人信息的功能。|
[ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|此类提供评审 API 的功能，包括用于创建作业、自定义工作流和人工评审的方法。|

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Python 的内容审查器客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [审查文本](#moderate-text)
* [使用自定义字词列表](#use-a-custom-terms-list)
* [审查图像](#moderate-images)
* [使用自定义图像列表](#use-a-custom-image-list)
* [创建评审](#create-a-review)

## <a name="authenticate-the-client"></a>验证客户端

> [!NOTE]
> 本快速入门假定你已为内容审查器密钥和终结点[创建了环境变量](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)。

使用终结点和密钥实例化某个客户端。 使用密钥创建 [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) 对象，然后在终结点上使用该对象创建 [ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python) 对象。

```python
client = ContentModeratorClient(
    endpoint=CONTENT_MODERATOR_ENDPOINT,
    credentials=CognitiveServicesCredentials(subscription_key)
)
```

## <a name="moderate-text"></a>审查文本

以下代码使用内容审查器客户端分析文本的正文，并将结果输出到控制台。 首先，在项目的根目录中创建 **text_files/** 文件夹，并在其中添加 *content_moderator_text_moderation.txt* 文件。 将你自己的文本添加到此文件中，或使用以下示例文本：

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

添加对新文件夹的引用。

```python
TEXT_FOLDER = os.path.join(os.path.dirname(
    os.path.realpath(__file__)), "text_files")
```

然后，将以下代码添加到 Python 脚本中。

```python
# Screen the input text: check for profanity,
# do autocorrect text, and check for personally identifying
# information (PII)
with open(os.path.join(TEXT_FOLDER, 'content_moderator_text_moderation.txt'), "rb") as text_fd:
    screen = client.text_moderation.screen_text(
        text_content_type="text/plain",
        text_content=text_fd,
        language="eng",
        autocorrect=True,
        pii=True
    )
    assert isinstance(screen, Screen)
    pprint(screen.as_dict())
```

## <a name="use-a-custom-terms-list"></a>使用自定义字词列表

以下代码演示如何管理用于审查文本的自定义字词列表。 可以使用 [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) 类创建字词列表、管理各个字词，并根据该列表筛选其他文本正文。

### <a name="get-sample-text"></a>获取示例文本

若要使用此示例，必须在项目的根目录中创建 **text_files/** 文件夹，并在其中添加 *content_moderator_term_list.txt* 文件。 此文件应包含要根据字词列表检查的有序文本。 可使用以下示例文本：

```
This text contains the terms "term1" and "term2".
```

添加对该文件夹的引用（如果尚未定义引用）。

```python
TEXT_FOLDER = os.path.join(os.path.dirname(
    os.path.realpath(__file__)), "text_files")
```

### <a name="create-a-list"></a>创建列表

将以下代码添加到 Python 脚本以创建自定义字词列表，并保存其 ID 值。

```python
#
# Create list
#
print("\nCreating list")
custom_list = client.list_management_term_lists.create(
    content_type="application/json",
    body={
        "name": "Term list name",
        "description": "Term list description",
    }
)
print("List created:")
assert isinstance(custom_list, TermList)
pprint(custom_list.as_dict())
list_id = custom_list.id
```

### <a name="define-list-details"></a>定义列表详细信息

可以使用列表的 ID 来编辑其名称和说明。

```python
#
# Update list details
#
print("\nUpdating details for list {}".format(list_id))
updated_list = client.list_management_term_lists.update(
    list_id=list_id,
    content_type="application/json",
    body={
        "name": "New name",
        "description": "New description"
    }
)
assert isinstance(updated_list, TermList)
pprint(updated_list.as_dict())
```

### <a name="add-a-term-to-the-list"></a>将字词添加到列表

以下代码将字词 `"term1"` 和 `"term2"` 添加到列表。

```python
#
# Add terms
#
print("\nAdding terms to list {}".format(list_id))
client.list_management_term.add_term(
    list_id=list_id,
    term="term1",
    language="eng"
)
client.list_management_term.add_term(
    list_id=list_id,
    term="term2",
    language="eng"
)
```

### <a name="get-all-terms-in-the-list"></a>获取列表中的所有字词

可以使用列表 ID 返回列表中的所有字词。

```python
#
# Get all terms ids
#
print("\nGetting all term IDs for list {}".format(list_id))
terms = client.list_management_term.get_all_terms(
    list_id=list_id, language="eng")
assert isinstance(terms, Terms)
terms_data = terms.data
assert isinstance(terms_data, TermsData)
pprint(terms_data.as_dict())
```

### <a name="refresh-the-list-index"></a>刷新列表索引

每当在列表中添加或删除字词后，都必须先刷新索引，然后才能使用更新的列表。

```python
#
# Refresh the index
#
print("\nRefreshing the search index for list {}".format(list_id))
refresh_index = client.list_management_term_lists.refresh_index_method(
    list_id=list_id, language="eng")
assert isinstance(refresh_index, RefreshIndex)
pprint(refresh_index.as_dict())

print("\nWaiting {} minutes to allow the server time to propagate the index changes.".format(
    LATENCY_DELAY))
time.sleep(LATENCY_DELAY * 60)
```

### <a name="screen-text-against-the-list"></a>根据列表筛选文本

自定义字词列表的主要功能就是将文本正文与该列表进行比较，找出是否存在任何匹配的字词。 

```python
#
# Screen text
#
with open(os.path.join(TEXT_FOLDER, 'content_moderator_term_list.txt'), "rb") as text_fd:
    screen = client.text_moderation.screen_text(
        text_content_type="text/plain",
        text_content=text_fd,
        language="eng",
        autocorrect=False,
        pii=False,
        list_id=list_id
    )
    assert isinstance(screen, Screen)
    pprint(screen.as_dict())
```

### <a name="remove-a-term-from-a-list"></a>从列表中删除字词

以下代码从列表中删除字词 `"term1"`。

```python
#
# Remove terms
#
term_to_remove = "term1"
print("\nRemove term {} from list {}".format(term_to_remove, list_id))
client.list_management_term.delete_term(
    list_id=list_id,
    term=term_to_remove,
    language="eng"
)
```

### <a name="remove-all-terms-from-a-list"></a>从列表中删除所有字词

使用以下代码可清除列表中的所有字词。

```python
#
# Delete all terms
#
print("\nDelete all terms in the image list {}".format(list_id))
client.list_management_term.delete_all_terms(
    list_id=list_id, language="eng")
```

### <a name="delete-list"></a>删除列表

使用以下代码可删除自定义字词列表。

```python
#
# Delete list
#
print("\nDelete the term list {}".format(list_id))
client.list_management_term_lists.delete(list_id=list_id)
```

## <a name="moderate-images"></a>审查图像

以下代码使用内容审查器客户端以及 [ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) 对象在图像中分析成人内容和猥亵内容。

### <a name="get-images"></a>获取图像

定义对要分析的某些图像的引用。

```python
IMAGE_LIST = [
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg",
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png"
]
```

然后添加以下代码来循环访问图像。 本部分的余下代码将进入此循环。

```python
for image_url in IMAGE_LIST:
    print("\nEvaluate image {}".format(image_url))
```

### <a name="check-for-adultracy-content"></a>检查成人/猥亵内容

以下代码在给定 URL 上的图像中检查成人内容或猥亵，然后将结果输出到控制台。 有关这些术语的含义，请参阅 [图像审查的概念](../../image-moderation-api.md) 指南。

```python
print("\nEvaluate for adult and racy content.")
evaluation = client.image_moderation.evaluate_url_input(
    content_type="application/json",
    cache_image=True,
    data_representation="URL",
    value=image_url
)
assert isinstance(evaluation, Evaluate)
pprint(evaluation.as_dict())
```

### <a name="check-for-visible-text"></a>检查可见文本

以下代码在图像中检查可见的文本内容，并将结果输出到控制台。

```python
print("\nDetect and extract text.")
evaluation = client.image_moderation.ocr_url_input(
    language="eng",
    content_type="application/json",
    data_representation="URL",
    value=image_url,
    cache_image=True,
)
assert isinstance(evaluation, OCR)
pprint(evaluation.as_dict())
```

### <a name="check-for-faces"></a>检查人脸

以下代码在图像中检查人脸，并将结果输出到控制台。

```python
print("\nDetect faces.")
evaluation = client.image_moderation.find_faces_url_input(
    content_type="application/json",
    cache_image=True,
    data_representation="URL",
    value=image_url
)
assert isinstance(evaluation, FoundFaces)
pprint(evaluation.as_dict())
```

## <a name="use-a-custom-image-list"></a>使用自定义图像列表

以下代码演示如何管理用于图像审查的自定义图像列表。 如果你的平台经常收到一组你要筛选出的相同图像的实例，则此功能很有用。维护这些特定图像的列表可以提高性能。 使用 [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) 类可以创建图像列表、管理该列表中的各个图像，并将这些图像与其他图像进行比较。

创建以下文本变量用于存储此方案中使用的图像 URL。

```python
IMAGE_LIST = {
    "Sports": [
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png",
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png",
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample9.png"
    ],
    "Swimsuit": [
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg",
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample3.png",
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png",
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png"
    ]
}

IMAGES_TO_MATCH = [
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg",
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png",
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png",
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png"
]
```

> [!NOTE]
> 此列表本身并不适当，而是要添加到代码的 `add images` 节中的非正式图像列表。


### <a name="create-an-image-list"></a>创建图像列表

添加以下代码以创建图像列表，并保存对其 ID 的引用。

```python
#
# Create list
#
print("Creating list MyList\n")
custom_list = client.list_management_image_lists.create(
    content_type="application/json",
    body={
        "name": "MyList",
        "description": "A sample list",
        "metadata": {
            "key_one": "Acceptable",
            "key_two": "Potentially racy"
        }
    }
)
print("List created:")
assert isinstance(custom_list, ImageList)
pprint(custom_list.as_dict())
list_id = custom_list.id
```

### <a name="add-images-to-a-list"></a>将图像添加到列表。

以下代码将所有图像添加到列表。

```python
print("\nAdding images to list {}".format(list_id))
index = {}  # Keep an index url to id for later removal
for label, urls in IMAGE_LIST.items():
    for url in urls:
        image = add_images(list_id, url, label)
        if image:
            index[url] = image.content_id
```

在脚本中的其他某个位置定义 **add_images** 帮助器函数。

```python
#
# Add images
#
def add_images(list_id, image_url, label):
    """Generic add_images from url and label."""
    print("\nAdding image {} to list {} with label {}.".format(
        image_url, list_id, label))
    try:
        added_image = client.list_management_image.add_image_url_input(
            list_id=list_id,
            content_type="application/json",
            data_representation="URL",
            value=image_url,
            label=label
        )
    except APIErrorException as err:
        # sample4 will fail
        print("Unable to add image to list: {}".format(err))
    else:
        assert isinstance(added_image, Image)
        pprint(added_image.as_dict())
        return added_image
```

### <a name="get-images-in-list"></a>获取列表中的图像

以下代码输出列表中所有图像的名称。

```python
#
# Get all images ids
#
print("\nGetting all image IDs for list {}".format(list_id))
image_ids = client.list_management_image.get_all_image_ids(list_id=list_id)
assert isinstance(image_ids, ImageIds)
pprint(image_ids.as_dict())
```

### <a name="update-list-details"></a>更新列表详细信息

可以使用列表 ID 来更新列表的名称和说明。

```python
#
# Update list details
#
print("\nUpdating details for list {}".format(list_id))
updated_list = client.list_management_image_lists.update(
    list_id=list_id,
    content_type="application/json",
    body={
        "name": "Swimsuits and sports"
    }
)
assert isinstance(updated_list, ImageList)
pprint(updated_list.as_dict())
```

### <a name="get-list-details"></a>获取列表详细信息

使用以下代码可输出列表的当前详细信息。

```python
#
# Get list details
#
print("\nGetting details for list {}".format(list_id))
list_details = client.list_management_image_lists.get_details(
    list_id=list_id)
assert isinstance(list_details, ImageList)
pprint(list_details.as_dict())
```

### <a name="refresh-the-list-index"></a>刷新列表索引

添加或删除图像后，必须刷新列表索引才能使用它来筛选其他图像。

```python
#
# Refresh the index
#
print("\nRefreshing the search index for list {}".format(list_id))
refresh_index = client.list_management_image_lists.refresh_index_method(
    list_id=list_id)
assert isinstance(refresh_index, RefreshIndex)
pprint(refresh_index.as_dict())

print("\nWaiting {} minutes to allow the server time to propagate the index changes.".format(
    LATENCY_DELAY))
time.sleep(LATENCY_DELAY * 60)
```

### <a name="match-images-against-the-list"></a>将图像与列表进行匹配

图像列表的主要功能是比较新图像，确定是否存在任何匹配项。

```python
#
# Match images against the image list.
#
for image_url in IMAGES_TO_MATCH:
    print("\nMatching image {} against list {}".format(image_url, list_id))
    match_result = client.image_moderation.match_url_input(
        content_type="application/json",
        list_id=list_id,
        data_representation="URL",
        value=image_url,
    )
    assert isinstance(match_result, MatchResponse)
    print("Is match? {}".format(match_result.is_match))
    print("Complete match details:")
    pprint(match_result.as_dict())
```

### <a name="remove-an-image-from-the-list"></a>从列表中删除图像

以下代码从列表中删除一个项。 在本例中，该项是与列表类别不匹配的某个图像。

```python
#
# Remove images
#
correction = "https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png"
print("\nRemove image {} from list {}".format(correction, list_id))
client.list_management_image.delete_image(
    list_id=list_id,
    image_id=index[correction]
)
```

### <a name="remove-all-images-from-a-list"></a>从列表中删除所有图像

使用以下代码可清除图像列表。

```python
#
# Delete all images
#
print("\nDelete all images in the image list {}".format(list_id))
client.list_management_image.delete_all_images(list_id=list_id)
```

### <a name="delete-the-image-list"></a>删除图像列表

使用以下代码可删除给定的图像列表。

```python
#
# Delete list
#
print("\nDelete the image list {}".format(list_id))
client.list_management_image_lists.delete(list_id=list_id)
```

<!--Not available in MC: Review Tool-->

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `python` 命令运行应用程序。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何使用内容审查器 Python 库来执行审查任务。 接下来，请阅读概念指南来详细了解图像或其他媒体的审查。

> [!div class="nextstepaction"]
>[图像审查的概念](https://docs.azure.cn/cognitive-services/content-moderator/image-moderation-api)

* [什么是 Azure 内容审查器？](../../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py) 上找到此示例的源代码。