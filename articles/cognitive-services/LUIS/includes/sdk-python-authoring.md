---
title: include 文件
description: include 文件
services: cognitive-services
author: Johnnytechn
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 12/01/2020
ms.topic: include
ms.custom: include file
ms.author: v-johya
ms.openlocfilehash: 1d788dc3fb7390ed45541316a1821c91cefaa04f
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96477998"
---
可以使用适用于 Python 的语言理解 (LUIS) 创作客户端库执行以下操作：

* 创建应用。
* 添加意向、实体和示例话语。
* 添加短语列表等特征。
* 训练和发布应用。

[参考文档](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [创作包 (Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建试用订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)
* 最新版本的 [Python 3.x](https://www.python.org/)。
* 有了 Azure 订阅后，在 Azure 门户中[创建语言理解创作资源](https://portal.azure.cn/#create/Microsoft.CognitiveServicesLUISAllInOne)，以获取创作密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从[创建](../luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal)的资源获取密钥和终结点，以便将应用程序连接到语言理解创作。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。 可以使用免费定价层 (`F0`) 来试用该服务。

## <a name="setting-up"></a>设置

### <a name="install-the-python-library-for-luis"></a>安装适用于 LUIS 的 Python 库

在应用程序目录中，使用以下命令安装适用于 Python 的语言理解 (LUIS) 创作客户端库：

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>对象模型

语言理解 (LUIS) 创作客户端是对 Azure 进行身份验证的 [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) 对象，其中包含创作密钥。

创建客户端后，可以使用此客户端访问如下所述的功能：

* 应用 - [创建](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-)、[删除](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-)、[发布](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* 示例言语 - [按批添加](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-)、[按 ID 删除](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-)
* 特征 - 管理[短语列表](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-)
* 模型 - 管理[意向](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-)和实体
* 模式 - 管理[模式](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-)
* 训练 - [训练](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-)应用和轮询[训练状态](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [版本](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python) - 使用克隆、导出和删除操作进行管理


## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Python 的语言理解 (LUIS) 创作客户端库来执行以下操作：

* [创建应用](#create-a-luis-app)
* [添加实体](#create-entities-for-the-app)
* [添加意向](#create-intent-for-the-app)
* [添加示例表述](#add-example-utterance-to-intent)
* [训练应用](#train-the-app)
* [发布应用](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

在首选编辑器或 IDE 中创建新的 Python 应用程序。 然后导入以下库。

```python
# Azure Language Understanding (LUIS) - Build App
#
# This script builds a LUIS app, entities, and intents using the Python
# LUIS SDK.  A separate sample trains and publishes the app.
#
# This script requires the Cognitive Services LUIS Python module:
#     python -m pip install azure-cognitiveservices-language-luis
#
# This script runs under Python 3.4 or later.

# Be sure you understand how LUIS models work.  In particular, know what
# intents, entities, and utterances are, and how they work together in the
# context of a LUIS app. See the following:
#
# https://luis.azure.cn/welcome
# /cognitive-services/luis/luis-concept-intent
# /cognitive-services/luis/luis-concept-entity-types
# /cognitive-services/luis/luis-concept-utterance

# <Dependencies>
from azure.cognitiveservices.language.luis.authoring import LUISAuthoringClient
from msrest.authentication import CognitiveServicesCredentials

import datetime, json, os, time
# </Dependencies>

# <AuthorizationVariables>
authoring_key = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY'

authoring_endpoint = "https://REPLACE-WITH-RESOURCE-NAME.cognitiveservices.azure.cn/"
# </AuthorizationVariables>

# <Client>
# Instantiate a LUIS client
client = LUISAuthoringClient(authoring_endpoint, CognitiveServicesCredentials(authoring_key))
# </Client>

# <createApp>
def create_app():
    # Create a new LUIS app
    app_name    = "Contoso {}".format(datetime.datetime.now())
    app_desc    = "Flight booking app built with LUIS Python SDK."
    app_version = "0.1"
    app_locale  = "en-us"

    app_id = client.apps.add(dict(name=app_name,
                                    initial_version_id=app_version,
                                    description=app_desc,
                                    culture=app_locale))

    print("Created LUIS app {}\n    with ID {}".format(app_name, app_id))
    return app_id, app_version
# </createApp>

# Declare entities:
#
#   Destination - A simple entity that will hold the flight destination
#
#   Class - A hierarchical entity that will hold the flight class
#           (First, Business, or Economy)
#
#   Flight - A composite entity represeting the flight (including
#               class and destination)
#
# Creating an entity (or other LUIS object) returns its ID.
# We don't use IDs further in this script, so we don't keep the return value.
# <addEntities>
def add_entities(app_id, app_version):

    destinationEntityId = client.model.add_entity(app_id, app_version, name="Destination")
    print("destinationEntityId {} added.".format(destinationEntityId))

    classEntityId = client.model.add_entity(app_id, app_version, name="Class")
    print("classEntityId {} added.".format(classEntityId))

    flightEntityId = client.model.add_entity(app_id, app_version, name="Flight")
    print("flightEntityId {} added.".format(flightEntityId))

# </addEntities>

# Declare an intent, FindFlights, that recognizes a user's Flight request
# Creating an intent returns its ID, which we don't need, so don't keep.
# <addIntents>
def add_intents(app_id, app_version):
    intentId = client.model.add_intent(app_id, app_version, "FindFlights")

    print("Intent FindFlights {} added.".format(intentId))
# </addIntents>


# Helper function for creating the utterance data structure.
# <createUtterance>
def create_utterance(intent, utterance, *labels):
    """Add an example LUIS utterance from utterance text and a list of
       labels.  Each label is a 2-tuple containing a label name and the
       text within the utterance that represents that label.

       Utterances apply to a specific intent, which must be specified."""

    text = utterance.lower()

    def label(name, value):
        value = value.lower()
        start = text.index(value)
        return dict(entity_name=name, start_char_index=start,
                    end_char_index=start + len(value))

    return dict(text=text, intent_name=intent,
                entity_labels=[label(n, v) for (n, v) in labels])
# </createUtterance>

# Add example utterances for the intent.  Each utterance includes labels
# that identify the entities within each utterance by index.  LUIS learns
# how to find entities within user utterances from the provided examples.
#
# Example utterance: "find flights in economy to Madrid"
# Labels: Flight -> "economy to Madrid" (composite of Destination and Class)
#         Destination -> "Madrid"
#         Class -> "economy"
# <addUtterances>
def add_utterances(app_id, app_version):
    # Now define the utterances
    utterances = [create_utterance("FindFlights", "find flights in economy to Madrid",
                            ("Flight", "economy to Madrid"),
                            ("Destination", "Madrid"),
                            ("Class", "economy")),

                  create_utterance("FindFlights", "find flights to London in first class",
                            ("Flight", "London in first class"),
                            ("Destination", "London"),
                            ("Class", "first")),

                  create_utterance("FindFlights", "find flights from seattle to London in first class",
                            ("Flight", "flights from seattle to London in first class"),
                            ("Destination", "London"),
                            ("Class", "first"))]

    # Add the utterances in batch. You may add any number of example utterances
    # for any number of intents in one call.
    client.examples.batch(app_id, app_version, utterances)
    print("{} example utterance(s) added.".format(len(utterances)))
# </addUtterances>

# <train>
def train_app(app_id, app_version):
    response = client.train.train_version(app_id, app_version)
    waiting = True
    while waiting:
        info = client.train.get_status(app_id, app_version)

        # get_status returns a list of training statuses, one for each model. Loop through them and make sure all are done.
        waiting = any(map(lambda x: 'Queued' == x.details.status or 'InProgress' == x.details.status, info))
        if waiting:
            print ("Waiting 10 seconds for training to complete...")
            time.sleep(10)
# </train>

# <publish>
def publish_app(app_id, app_version):
    responseEndpointInfo = client.apps.publish(app_id, app_version, is_staging=True)
    print("Application published. Endpoint URL: " + responseEndpointInfo.endpoint_url)
# </publish>

# <predict>
def predict(app_id, publishInfo, slot_name):

    request = { "query" : "Find flight to seattle" }

    # Note be sure to specify, using the slot_name parameter, whether your application is in staging or production.
    response = clientRuntime.prediction.get_slot_prediction(app_id=app_id, slot_name=slot_name, prediction_request=request)

    print("Top intent: {}".format(response.prediction.top_intent))
    print("Sentiment: {}".format (response.prediction.sentiment))
    print("Intents: ")

    for intent in response.prediction.intents:
        print("\t{}".format (json.dumps (intent)))
    print("Entities: {}".format (response.prediction.entities))
# </predict>

print("Creating application...")
app_id, app_version = create_app()
print()

print ("Adding entities to application...")
add_entities(app_id, app_version)
print ()

print ("Adding intents to application...")
add_intents(app_id, app_version)
print ()

print ("Adding utterances to application...")
add_utterances(app_id, app_version)
print ()

print ("Training application...")
train_app(app_id, app_version)
print ()

print ("Publishing application...")
publish_app(app_id, app_version)
```

```python
# Azure Language Understanding (LUIS) - Build App
#
# This script builds a LUIS app, entities, and intents using the Python
# LUIS SDK.  A separate sample trains and publishes the app.
#
# This script requires the Cognitive Services LUIS Python module:
#     python -m pip install azure-cognitiveservices-language-luis
#
# This script runs under Python 3.4 or later.

# Be sure you understand how LUIS models work.  In particular, know what
# intents, entities, and utterances are, and how they work together in the
# context of a LUIS app. See the following:
#
# https://luis.azure.cn/welcome
# /cognitive-services/luis/luis-concept-intent
# /cognitive-services/luis/luis-concept-entity-types
# /cognitive-services/luis/luis-concept-utterance

# <Dependencies>
from azure.cognitiveservices.language.luis.authoring import LUISAuthoringClient
from msrest.authentication import CognitiveServicesCredentials

import datetime, json, os, time
# </Dependencies>

# <AuthorizationVariables>
authoring_key = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY'

authoring_endpoint = "https://REPLACE-WITH-RESOURCE-NAME.cognitiveservices.azure.cn/"
# </AuthorizationVariables>

# <Client>
# Instantiate a LUIS client
client = LUISAuthoringClient(authoring_endpoint, CognitiveServicesCredentials(authoring_key))
# </Client>

# <createApp>
def create_app():
    # Create a new LUIS app
    app_name    = "Contoso {}".format(datetime.datetime.now())
    app_desc    = "Flight booking app built with LUIS Python SDK."
    app_version = "0.1"
    app_locale  = "en-us"

    app_id = client.apps.add(dict(name=app_name,
                                    initial_version_id=app_version,
                                    description=app_desc,
                                    culture=app_locale))

    print("Created LUIS app {}\n    with ID {}".format(app_name, app_id))
    return app_id, app_version
# </createApp>

# Declare entities:
#
#   Destination - A simple entity that will hold the flight destination
#
#   Class - A hierarchical entity that will hold the flight class
#           (First, Business, or Economy)
#
#   Flight - A composite entity represeting the flight (including
#               class and destination)
#
# Creating an entity (or other LUIS object) returns its ID.
# We don't use IDs further in this script, so we don't keep the return value.
# <addEntities>
def add_entities(app_id, app_version):

    destinationEntityId = client.model.add_entity(app_id, app_version, name="Destination")
    print("destinationEntityId {} added.".format(destinationEntityId))

    classEntityId = client.model.add_entity(app_id, app_version, name="Class")
    print("classEntityId {} added.".format(classEntityId))

    flightEntityId = client.model.add_entity(app_id, app_version, name="Flight")
    print("flightEntityId {} added.".format(flightEntityId))

# </addEntities>

# Declare an intent, FindFlights, that recognizes a user's Flight request
# Creating an intent returns its ID, which we don't need, so don't keep.
# <addIntents>
def add_intents(app_id, app_version):
    intentId = client.model.add_intent(app_id, app_version, "FindFlights")

    print("Intent FindFlights {} added.".format(intentId))
# </addIntents>


# Helper function for creating the utterance data structure.
# <createUtterance>
def create_utterance(intent, utterance, *labels):
    """Add an example LUIS utterance from utterance text and a list of
       labels.  Each label is a 2-tuple containing a label name and the
       text within the utterance that represents that label.

       Utterances apply to a specific intent, which must be specified."""

    text = utterance.lower()

    def label(name, value):
        value = value.lower()
        start = text.index(value)
        return dict(entity_name=name, start_char_index=start,
                    end_char_index=start + len(value))

    return dict(text=text, intent_name=intent,
                entity_labels=[label(n, v) for (n, v) in labels])
# </createUtterance>

# Add example utterances for the intent.  Each utterance includes labels
# that identify the entities within each utterance by index.  LUIS learns
# how to find entities within user utterances from the provided examples.
#
# Example utterance: "find flights in economy to Madrid"
# Labels: Flight -> "economy to Madrid" (composite of Destination and Class)
#         Destination -> "Madrid"
#         Class -> "economy"
# <addUtterances>
def add_utterances(app_id, app_version):
    # Now define the utterances
    utterances = [create_utterance("FindFlights", "find flights in economy to Madrid",
                            ("Flight", "economy to Madrid"),
                            ("Destination", "Madrid"),
                            ("Class", "economy")),

                  create_utterance("FindFlights", "find flights to London in first class",
                            ("Flight", "London in first class"),
                            ("Destination", "London"),
                            ("Class", "first")),

                  create_utterance("FindFlights", "find flights from seattle to London in first class",
                            ("Flight", "flights from seattle to London in first class"),
                            ("Destination", "London"),
                            ("Class", "first"))]

    # Add the utterances in batch. You may add any number of example utterances
    # for any number of intents in one call.
    client.examples.batch(app_id, app_version, utterances)
    print("{} example utterance(s) added.".format(len(utterances)))
# </addUtterances>

# <train>
def train_app(app_id, app_version):
    response = client.train.train_version(app_id, app_version)
    waiting = True
    while waiting:
        info = client.train.get_status(app_id, app_version)

        # get_status returns a list of training statuses, one for each model. Loop through them and make sure all are done.
        waiting = any(map(lambda x: 'Queued' == x.details.status or 'InProgress' == x.details.status, info))
        if waiting:
            print ("Waiting 10 seconds for training to complete...")
            time.sleep(10)
# </train>

# <publish>
def publish_app(app_id, app_version):
    responseEndpointInfo = client.apps.publish(app_id, app_version, is_staging=True)
    print("Application published. Endpoint URL: " + responseEndpointInfo.endpoint_url)
# </publish>

# <predict>
def predict(app_id, publishInfo, slot_name):

    request = { "query" : "Find flight to seattle" }

    # Note be sure to specify, using the slot_name parameter, whether your application is in staging or production.
    response = clientRuntime.prediction.get_slot_prediction(app_id=app_id, slot_name=slot_name, prediction_request=request)

    print("Top intent: {}".format(response.prediction.top_intent))
    print("Sentiment: {}".format (response.prediction.sentiment))
    print("Intents: ")

    for intent in response.prediction.intents:
        print("\t{}".format (json.dumps (intent)))
    print("Entities: {}".format (response.prediction.entities))
# </predict>

print("Creating application...")
app_id, app_version = create_app()
print()

print ("Adding entities to application...")
add_entities(app_id, app_version)
print ()

print ("Adding intents to application...")
add_intents(app_id, app_version)
print ()

print ("Adding utterances to application...")
add_utterances(app_id, app_version)
print ()

print ("Training application...")
train_app(app_id, app_version)
print ()

print ("Publishing application...")
publish_app(app_id, app_version)
```

## <a name="authenticate-the-client"></a>验证客户端

使用密钥创建 [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) 对象，并在终结点中使用该对象创建一个 [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) 对象。

```python
# Azure Language Understanding (LUIS) - Build App
#
# This script builds a LUIS app, entities, and intents using the Python
# LUIS SDK.  A separate sample trains and publishes the app.
#
# This script requires the Cognitive Services LUIS Python module:
#     python -m pip install azure-cognitiveservices-language-luis
#
# This script runs under Python 3.4 or later.

# Be sure you understand how LUIS models work.  In particular, know what
# intents, entities, and utterances are, and how they work together in the
# context of a LUIS app. See the following:
#
# https://luis.azure.cn/welcome
# /cognitive-services/luis/luis-concept-intent
# /cognitive-services/luis/luis-concept-entity-types
# /cognitive-services/luis/luis-concept-utterance

# <Dependencies>
from azure.cognitiveservices.language.luis.authoring import LUISAuthoringClient
from msrest.authentication import CognitiveServicesCredentials

import datetime, json, os, time
# </Dependencies>

# <AuthorizationVariables>
authoring_key = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY'

authoring_endpoint = "https://REPLACE-WITH-RESOURCE-NAME.cognitiveservices.azure.cn/"
# </AuthorizationVariables>

# <Client>
# Instantiate a LUIS client
client = LUISAuthoringClient(authoring_endpoint, CognitiveServicesCredentials(authoring_key))
# </Client>

# <createApp>
def create_app():
    # Create a new LUIS app
    app_name    = "Contoso {}".format(datetime.datetime.now())
    app_desc    = "Flight booking app built with LUIS Python SDK."
    app_version = "0.1"
    app_locale  = "en-us"

    app_id = client.apps.add(dict(name=app_name,
                                    initial_version_id=app_version,
                                    description=app_desc,
                                    culture=app_locale))

    print("Created LUIS app {}\n    with ID {}".format(app_name, app_id))
    return app_id, app_version
# </createApp>

# Declare entities:
#
#   Destination - A simple entity that will hold the flight destination
#
#   Class - A hierarchical entity that will hold the flight class
#           (First, Business, or Economy)
#
#   Flight - A composite entity represeting the flight (including
#               class and destination)
#
# Creating an entity (or other LUIS object) returns its ID.
# We don't use IDs further in this script, so we don't keep the return value.
# <addEntities>
def add_entities(app_id, app_version):

    destinationEntityId = client.model.add_entity(app_id, app_version, name="Destination")
    print("destinationEntityId {} added.".format(destinationEntityId))

    classEntityId = client.model.add_entity(app_id, app_version, name="Class")
    print("classEntityId {} added.".format(classEntityId))

    flightEntityId = client.model.add_entity(app_id, app_version, name="Flight")
    print("flightEntityId {} added.".format(flightEntityId))

# </addEntities>

# Declare an intent, FindFlights, that recognizes a user's Flight request
# Creating an intent returns its ID, which we don't need, so don't keep.
# <addIntents>
def add_intents(app_id, app_version):
    intentId = client.model.add_intent(app_id, app_version, "FindFlights")

    print("Intent FindFlights {} added.".format(intentId))
# </addIntents>


# Helper function for creating the utterance data structure.
# <createUtterance>
def create_utterance(intent, utterance, *labels):
    """Add an example LUIS utterance from utterance text and a list of
       labels.  Each label is a 2-tuple containing a label name and the
       text within the utterance that represents that label.

       Utterances apply to a specific intent, which must be specified."""

    text = utterance.lower()

    def label(name, value):
        value = value.lower()
        start = text.index(value)
        return dict(entity_name=name, start_char_index=start,
                    end_char_index=start + len(value))

    return dict(text=text, intent_name=intent,
                entity_labels=[label(n, v) for (n, v) in labels])
# </createUtterance>

# Add example utterances for the intent.  Each utterance includes labels
# that identify the entities within each utterance by index.  LUIS learns
# how to find entities within user utterances from the provided examples.
#
# Example utterance: "find flights in economy to Madrid"
# Labels: Flight -> "economy to Madrid" (composite of Destination and Class)
#         Destination -> "Madrid"
#         Class -> "economy"
# <addUtterances>
def add_utterances(app_id, app_version):
    # Now define the utterances
    utterances = [create_utterance("FindFlights", "find flights in economy to Madrid",
                            ("Flight", "economy to Madrid"),
                            ("Destination", "Madrid"),
                            ("Class", "economy")),

                  create_utterance("FindFlights", "find flights to London in first class",
                            ("Flight", "London in first class"),
                            ("Destination", "London"),
                            ("Class", "first")),

                  create_utterance("FindFlights", "find flights from seattle to London in first class",
                            ("Flight", "flights from seattle to London in first class"),
                            ("Destination", "London"),
                            ("Class", "first"))]

    # Add the utterances in batch. You may add any number of example utterances
    # for any number of intents in one call.
    client.examples.batch(app_id, app_version, utterances)
    print("{} example utterance(s) added.".format(len(utterances)))
# </addUtterances>

# <train>
def train_app(app_id, app_version):
    response = client.train.train_version(app_id, app_version)
    waiting = True
    while waiting:
        info = client.train.get_status(app_id, app_version)

        # get_status returns a list of training statuses, one for each model. Loop through them and make sure all are done.
        waiting = any(map(lambda x: 'Queued' == x.details.status or 'InProgress' == x.details.status, info))
        if waiting:
            print ("Waiting 10 seconds for training to complete...")
            time.sleep(10)
# </train>

# <publish>
def publish_app(app_id, app_version):
    responseEndpointInfo = client.apps.publish(app_id, app_version, is_staging=True)
    print("Application published. Endpoint URL: " + responseEndpointInfo.endpoint_url)
# </publish>

# <predict>
def predict(app_id, publishInfo, slot_name):

    request = { "query" : "Find flight to seattle" }

    # Note be sure to specify, using the slot_name parameter, whether your application is in staging or production.
    response = clientRuntime.prediction.get_slot_prediction(app_id=app_id, slot_name=slot_name, prediction_request=request)

    print("Top intent: {}".format(response.prediction.top_intent))
    print("Sentiment: {}".format (response.prediction.sentiment))
    print("Intents: ")

    for intent in response.prediction.intents:
        print("\t{}".format (json.dumps (intent)))
    print("Entities: {}".format (response.prediction.entities))
# </predict>

print("Creating application...")
app_id, app_version = create_app()
print()

print ("Adding entities to application...")
add_entities(app_id, app_version)
print ()

print ("Adding intents to application...")
add_intents(app_id, app_version)
print ()

print ("Adding utterances to application...")
add_utterances(app_id, app_version)
print ()

print ("Training application...")
train_app(app_id, app_version)
print ()

print ("Publishing application...")
publish_app(app_id, app_version)
```

## <a name="create-a-luis-app"></a>创建 LUIS 应用

1. 创建一个 LUIS 应用，用于包含保存意向、实体和示例言语的自然语言处理 (NLP) 模型。

1. 创建 [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) 对象的 [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) 方法，用于创建应用。 名称和语言区域性是必需的属性。

```python
# Azure Language Understanding (LUIS) - Build App
#
# This script builds a LUIS app, entities, and intents using the Python
# LUIS SDK.  A separate sample trains and publishes the app.
#
# This script requires the Cognitive Services LUIS Python module:
#     python -m pip install azure-cognitiveservices-language-luis
#
# This script runs under Python 3.4 or later.

# Be sure you understand how LUIS models work.  In particular, know what
# intents, entities, and utterances are, and how they work together in the
# context of a LUIS app. See the following:
#
# https://luis.azure.cn/welcome
# /cognitive-services/luis/luis-concept-intent
# /cognitive-services/luis/luis-concept-entity-types
# /cognitive-services/luis/luis-concept-utterance

# <Dependencies>
from azure.cognitiveservices.language.luis.authoring import LUISAuthoringClient
from msrest.authentication import CognitiveServicesCredentials

import datetime, json, os, time
# </Dependencies>

# <AuthorizationVariables>
authoring_key = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY'

authoring_endpoint = "https://REPLACE-WITH-RESOURCE-NAME.cognitiveservices.azure.cn/"
# </AuthorizationVariables>

# <Client>
# Instantiate a LUIS client
client = LUISAuthoringClient(authoring_endpoint, CognitiveServicesCredentials(authoring_key))
# </Client>

# <createApp>
def create_app():
    # Create a new LUIS app
    app_name    = "Contoso {}".format(datetime.datetime.now())
    app_desc    = "Flight booking app built with LUIS Python SDK."
    app_version = "0.1"
    app_locale  = "en-us"

    app_id = client.apps.add(dict(name=app_name,
                                    initial_version_id=app_version,
                                    description=app_desc,
                                    culture=app_locale))

    print("Created LUIS app {}\n    with ID {}".format(app_name, app_id))
    return app_id, app_version
# </createApp>

# Declare entities:
#
#   Destination - A simple entity that will hold the flight destination
#
#   Class - A hierarchical entity that will hold the flight class
#           (First, Business, or Economy)
#
#   Flight - A composite entity represeting the flight (including
#               class and destination)
#
# Creating an entity (or other LUIS object) returns its ID.
# We don't use IDs further in this script, so we don't keep the return value.
# <addEntities>
def add_entities(app_id, app_version):

    destinationEntityId = client.model.add_entity(app_id, app_version, name="Destination")
    print("destinationEntityId {} added.".format(destinationEntityId))

    classEntityId = client.model.add_entity(app_id, app_version, name="Class")
    print("classEntityId {} added.".format(classEntityId))

    flightEntityId = client.model.add_entity(app_id, app_version, name="Flight")
    print("flightEntityId {} added.".format(flightEntityId))

# </addEntities>

# Declare an intent, FindFlights, that recognizes a user's Flight request
# Creating an intent returns its ID, which we don't need, so don't keep.
# <addIntents>
def add_intents(app_id, app_version):
    intentId = client.model.add_intent(app_id, app_version, "FindFlights")

    print("Intent FindFlights {} added.".format(intentId))
# </addIntents>


# Helper function for creating the utterance data structure.
# <createUtterance>
def create_utterance(intent, utterance, *labels):
    """Add an example LUIS utterance from utterance text and a list of
       labels.  Each label is a 2-tuple containing a label name and the
       text within the utterance that represents that label.

       Utterances apply to a specific intent, which must be specified."""

    text = utterance.lower()

    def label(name, value):
        value = value.lower()
        start = text.index(value)
        return dict(entity_name=name, start_char_index=start,
                    end_char_index=start + len(value))

    return dict(text=text, intent_name=intent,
                entity_labels=[label(n, v) for (n, v) in labels])
# </createUtterance>

# Add example utterances for the intent.  Each utterance includes labels
# that identify the entities within each utterance by index.  LUIS learns
# how to find entities within user utterances from the provided examples.
#
# Example utterance: "find flights in economy to Madrid"
# Labels: Flight -> "economy to Madrid" (composite of Destination and Class)
#         Destination -> "Madrid"
#         Class -> "economy"
# <addUtterances>
def add_utterances(app_id, app_version):
    # Now define the utterances
    utterances = [create_utterance("FindFlights", "find flights in economy to Madrid",
                            ("Flight", "economy to Madrid"),
                            ("Destination", "Madrid"),
                            ("Class", "economy")),

                  create_utterance("FindFlights", "find flights to London in first class",
                            ("Flight", "London in first class"),
                            ("Destination", "London"),
                            ("Class", "first")),

                  create_utterance("FindFlights", "find flights from seattle to London in first class",
                            ("Flight", "flights from seattle to London in first class"),
                            ("Destination", "London"),
                            ("Class", "first"))]

    # Add the utterances in batch. You may add any number of example utterances
    # for any number of intents in one call.
    client.examples.batch(app_id, app_version, utterances)
    print("{} example utterance(s) added.".format(len(utterances)))
# </addUtterances>

# <train>
def train_app(app_id, app_version):
    response = client.train.train_version(app_id, app_version)
    waiting = True
    while waiting:
        info = client.train.get_status(app_id, app_version)

        # get_status returns a list of training statuses, one for each model. Loop through them and make sure all are done.
        waiting = any(map(lambda x: 'Queued' == x.details.status or 'InProgress' == x.details.status, info))
        if waiting:
            print ("Waiting 10 seconds for training to complete...")
            time.sleep(10)
# </train>

# <publish>
def publish_app(app_id, app_version):
    responseEndpointInfo = client.apps.publish(app_id, app_version, is_staging=True)
    print("Application published. Endpoint URL: " + responseEndpointInfo.endpoint_url)
# </publish>

# <predict>
def predict(app_id, publishInfo, slot_name):

    request = { "query" : "Find flight to seattle" }

    # Note be sure to specify, using the slot_name parameter, whether your application is in staging or production.
    response = clientRuntime.prediction.get_slot_prediction(app_id=app_id, slot_name=slot_name, prediction_request=request)

    print("Top intent: {}".format(response.prediction.top_intent))
    print("Sentiment: {}".format (response.prediction.sentiment))
    print("Intents: ")

    for intent in response.prediction.intents:
        print("\t{}".format (json.dumps (intent)))
    print("Entities: {}".format (response.prediction.entities))
# </predict>

print("Creating application...")
app_id, app_version = create_app()
print()

print ("Adding entities to application...")
add_entities(app_id, app_version)
print ()

print ("Adding intents to application...")
add_intents(app_id, app_version)
print ()

print ("Adding utterances to application...")
add_utterances(app_id, app_version)
print ()

print ("Training application...")
train_app(app_id, app_version)
print ()

print ("Publishing application...")
publish_app(app_id, app_version)
```


## <a name="create-intent-for-the-app"></a>为应用创建意向
LUIS 应用模型中的主要对象是意向。 意向与用户言语意向的分组相符。 用户可以提问，或者做出表述，指出希望机器人（或其他客户端应用程序）提供特定的有针对性响应。 意向的示例包括预订航班、询问目的地城市的天气，以及询问客户服务的联系信息。

将 [model.add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) 方法与唯一意向的名称配合使用，然后传递应用 ID、版本 ID 和新的意向名称。

```python
# Azure Language Understanding (LUIS) - Build App
#
# This script builds a LUIS app, entities, and intents using the Python
# LUIS SDK.  A separate sample trains and publishes the app.
#
# This script requires the Cognitive Services LUIS Python module:
#     python -m pip install azure-cognitiveservices-language-luis
#
# This script runs under Python 3.4 or later.

# Be sure you understand how LUIS models work.  In particular, know what
# intents, entities, and utterances are, and how they work together in the
# context of a LUIS app. See the following:
#
# https://luis.azure.cn/welcome
# /cognitive-services/luis/luis-concept-intent
# /cognitive-services/luis/luis-concept-entity-types
# /cognitive-services/luis/luis-concept-utterance

# <Dependencies>
from azure.cognitiveservices.language.luis.authoring import LUISAuthoringClient
from msrest.authentication import CognitiveServicesCredentials

import datetime, json, os, time
# </Dependencies>

# <AuthorizationVariables>
authoring_key = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY'

authoring_endpoint = "https://REPLACE-WITH-RESOURCE-NAME.cognitiveservices.azure.cn/"
# </AuthorizationVariables>

# <Client>
# Instantiate a LUIS client
client = LUISAuthoringClient(authoring_endpoint, CognitiveServicesCredentials(authoring_key))
# </Client>

# <createApp>
def create_app():
    # Create a new LUIS app
    app_name    = "Contoso {}".format(datetime.datetime.now())
    app_desc    = "Flight booking app built with LUIS Python SDK."
    app_version = "0.1"
    app_locale  = "en-us"

    app_id = client.apps.add(dict(name=app_name,
                                    initial_version_id=app_version,
                                    description=app_desc,
                                    culture=app_locale))

    print("Created LUIS app {}\n    with ID {}".format(app_name, app_id))
    return app_id, app_version
# </createApp>

# Declare entities:
#
#   Destination - A simple entity that will hold the flight destination
#
#   Class - A hierarchical entity that will hold the flight class
#           (First, Business, or Economy)
#
#   Flight - A composite entity represeting the flight (including
#               class and destination)
#
# Creating an entity (or other LUIS object) returns its ID.
# We don't use IDs further in this script, so we don't keep the return value.
# <addEntities>
def add_entities(app_id, app_version):

    destinationEntityId = client.model.add_entity(app_id, app_version, name="Destination")
    print("destinationEntityId {} added.".format(destinationEntityId))

    classEntityId = client.model.add_entity(app_id, app_version, name="Class")
    print("classEntityId {} added.".format(classEntityId))

    flightEntityId = client.model.add_entity(app_id, app_version, name="Flight")
    print("flightEntityId {} added.".format(flightEntityId))

# </addEntities>

# Declare an intent, FindFlights, that recognizes a user's Flight request
# Creating an intent returns its ID, which we don't need, so don't keep.
# <addIntents>
def add_intents(app_id, app_version):
    intentId = client.model.add_intent(app_id, app_version, "FindFlights")

    print("Intent FindFlights {} added.".format(intentId))
# </addIntents>


# Helper function for creating the utterance data structure.
# <createUtterance>
def create_utterance(intent, utterance, *labels):
    """Add an example LUIS utterance from utterance text and a list of
       labels.  Each label is a 2-tuple containing a label name and the
       text within the utterance that represents that label.

       Utterances apply to a specific intent, which must be specified."""

    text = utterance.lower()

    def label(name, value):
        value = value.lower()
        start = text.index(value)
        return dict(entity_name=name, start_char_index=start,
                    end_char_index=start + len(value))

    return dict(text=text, intent_name=intent,
                entity_labels=[label(n, v) for (n, v) in labels])
# </createUtterance>

# Add example utterances for the intent.  Each utterance includes labels
# that identify the entities within each utterance by index.  LUIS learns
# how to find entities within user utterances from the provided examples.
#
# Example utterance: "find flights in economy to Madrid"
# Labels: Flight -> "economy to Madrid" (composite of Destination and Class)
#         Destination -> "Madrid"
#         Class -> "economy"
# <addUtterances>
def add_utterances(app_id, app_version):
    # Now define the utterances
    utterances = [create_utterance("FindFlights", "find flights in economy to Madrid",
                            ("Flight", "economy to Madrid"),
                            ("Destination", "Madrid"),
                            ("Class", "economy")),

                  create_utterance("FindFlights", "find flights to London in first class",
                            ("Flight", "London in first class"),
                            ("Destination", "London"),
                            ("Class", "first")),

                  create_utterance("FindFlights", "find flights from seattle to London in first class",
                            ("Flight", "flights from seattle to London in first class"),
                            ("Destination", "London"),
                            ("Class", "first"))]

    # Add the utterances in batch. You may add any number of example utterances
    # for any number of intents in one call.
    client.examples.batch(app_id, app_version, utterances)
    print("{} example utterance(s) added.".format(len(utterances)))
# </addUtterances>

# <train>
def train_app(app_id, app_version):
    response = client.train.train_version(app_id, app_version)
    waiting = True
    while waiting:
        info = client.train.get_status(app_id, app_version)

        # get_status returns a list of training statuses, one for each model. Loop through them and make sure all are done.
        waiting = any(map(lambda x: 'Queued' == x.details.status or 'InProgress' == x.details.status, info))
        if waiting:
            print ("Waiting 10 seconds for training to complete...")
            time.sleep(10)
# </train>

# <publish>
def publish_app(app_id, app_version):
    responseEndpointInfo = client.apps.publish(app_id, app_version, is_staging=True)
    print("Application published. Endpoint URL: " + responseEndpointInfo.endpoint_url)
# </publish>

# <predict>
def predict(app_id, publishInfo, slot_name):

    request = { "query" : "Find flight to seattle" }

    # Note be sure to specify, using the slot_name parameter, whether your application is in staging or production.
    response = clientRuntime.prediction.get_slot_prediction(app_id=app_id, slot_name=slot_name, prediction_request=request)

    print("Top intent: {}".format(response.prediction.top_intent))
    print("Sentiment: {}".format (response.prediction.sentiment))
    print("Intents: ")

    for intent in response.prediction.intents:
        print("\t{}".format (json.dumps (intent)))
    print("Entities: {}".format (response.prediction.entities))
# </predict>

print("Creating application...")
app_id, app_version = create_app()
print()

print ("Adding entities to application...")
add_entities(app_id, app_version)
print ()

print ("Adding intents to application...")
add_intents(app_id, app_version)
print ()

print ("Adding utterances to application...")
add_utterances(app_id, app_version)
print ()

print ("Training application...")
train_app(app_id, app_version)
print ()

print ("Publishing application...")
publish_app(app_id, app_version)
```

## <a name="create-entities-for-the-app"></a>为应用创建实体

尽管实体不是必需的，但在大多数应用中都可以看到实体。 实体从用户言语中提取信息，只有使用这些信息才能实现用户的意向。 有多种类型的[预生成](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-)实体和自定义实体，每种实体具有自身的数据转换对象 (DTO) 模型。  在应用中添加的常见预生成实体包括 [number](../luis-reference-prebuilt-number.md)、[datetimeV2](../luis-reference-prebuilt-datetimev2.md)、[geographyV2](../luis-reference-prebuilt-geographyv2.md) 和 [ordinal](../luis-reference-prebuilt-ordinal.md)。

此 **add_entities** 方法创建一个包含两个角色的 `Location` 简单实体、一个 `Class` 简单实体和一个 `Flight` 复合实体，并添加多个预生成实体。

必须知道，实体不会使用意向进行标记。 它们可以并且通常应用到多个意向。 只会为特定的单个意向标记示例用户言语。

实体的创建方法属于 [ModelOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python) 类的一部分。 每个实体类型有自身的数据转换对象 (DTO) 模型。

```python
# Azure Language Understanding (LUIS) - Build App
#
# This script builds a LUIS app, entities, and intents using the Python
# LUIS SDK.  A separate sample trains and publishes the app.
#
# This script requires the Cognitive Services LUIS Python module:
#     python -m pip install azure-cognitiveservices-language-luis
#
# This script runs under Python 3.4 or later.

# Be sure you understand how LUIS models work.  In particular, know what
# intents, entities, and utterances are, and how they work together in the
# context of a LUIS app. See the following:
#
# https://luis.azure.cn/welcome
# /cognitive-services/luis/luis-concept-intent
# /cognitive-services/luis/luis-concept-entity-types
# /cognitive-services/luis/luis-concept-utterance

# <Dependencies>
from azure.cognitiveservices.language.luis.authoring import LUISAuthoringClient
from msrest.authentication import CognitiveServicesCredentials

import datetime, json, os, time
# </Dependencies>

# <AuthorizationVariables>
authoring_key = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY'

authoring_endpoint = "https://REPLACE-WITH-RESOURCE-NAME.cognitiveservices.azure.cn/"
# </AuthorizationVariables>

# <Client>
# Instantiate a LUIS client
client = LUISAuthoringClient(authoring_endpoint, CognitiveServicesCredentials(authoring_key))
# </Client>

# <createApp>
def create_app():
    # Create a new LUIS app
    app_name    = "Contoso {}".format(datetime.datetime.now())
    app_desc    = "Flight booking app built with LUIS Python SDK."
    app_version = "0.1"
    app_locale  = "en-us"

    app_id = client.apps.add(dict(name=app_name,
                                    initial_version_id=app_version,
                                    description=app_desc,
                                    culture=app_locale))

    print("Created LUIS app {}\n    with ID {}".format(app_name, app_id))
    return app_id, app_version
# </createApp>

# Declare entities:
#
#   Destination - A simple entity that will hold the flight destination
#
#   Class - A hierarchical entity that will hold the flight class
#           (First, Business, or Economy)
#
#   Flight - A composite entity represeting the flight (including
#               class and destination)
#
# Creating an entity (or other LUIS object) returns its ID.
# We don't use IDs further in this script, so we don't keep the return value.
# <addEntities>
def add_entities(app_id, app_version):

    destinationEntityId = client.model.add_entity(app_id, app_version, name="Destination")
    print("destinationEntityId {} added.".format(destinationEntityId))

    classEntityId = client.model.add_entity(app_id, app_version, name="Class")
    print("classEntityId {} added.".format(classEntityId))

    flightEntityId = client.model.add_entity(app_id, app_version, name="Flight")
    print("flightEntityId {} added.".format(flightEntityId))

# </addEntities>

# Declare an intent, FindFlights, that recognizes a user's Flight request
# Creating an intent returns its ID, which we don't need, so don't keep.
# <addIntents>
def add_intents(app_id, app_version):
    intentId = client.model.add_intent(app_id, app_version, "FindFlights")

    print("Intent FindFlights {} added.".format(intentId))
# </addIntents>


# Helper function for creating the utterance data structure.
# <createUtterance>
def create_utterance(intent, utterance, *labels):
    """Add an example LUIS utterance from utterance text and a list of
       labels.  Each label is a 2-tuple containing a label name and the
       text within the utterance that represents that label.

       Utterances apply to a specific intent, which must be specified."""

    text = utterance.lower()

    def label(name, value):
        value = value.lower()
        start = text.index(value)
        return dict(entity_name=name, start_char_index=start,
                    end_char_index=start + len(value))

    return dict(text=text, intent_name=intent,
                entity_labels=[label(n, v) for (n, v) in labels])
# </createUtterance>

# Add example utterances for the intent.  Each utterance includes labels
# that identify the entities within each utterance by index.  LUIS learns
# how to find entities within user utterances from the provided examples.
#
# Example utterance: "find flights in economy to Madrid"
# Labels: Flight -> "economy to Madrid" (composite of Destination and Class)
#         Destination -> "Madrid"
#         Class -> "economy"
# <addUtterances>
def add_utterances(app_id, app_version):
    # Now define the utterances
    utterances = [create_utterance("FindFlights", "find flights in economy to Madrid",
                            ("Flight", "economy to Madrid"),
                            ("Destination", "Madrid"),
                            ("Class", "economy")),

                  create_utterance("FindFlights", "find flights to London in first class",
                            ("Flight", "London in first class"),
                            ("Destination", "London"),
                            ("Class", "first")),

                  create_utterance("FindFlights", "find flights from seattle to London in first class",
                            ("Flight", "flights from seattle to London in first class"),
                            ("Destination", "London"),
                            ("Class", "first"))]

    # Add the utterances in batch. You may add any number of example utterances
    # for any number of intents in one call.
    client.examples.batch(app_id, app_version, utterances)
    print("{} example utterance(s) added.".format(len(utterances)))
# </addUtterances>

# <train>
def train_app(app_id, app_version):
    response = client.train.train_version(app_id, app_version)
    waiting = True
    while waiting:
        info = client.train.get_status(app_id, app_version)

        # get_status returns a list of training statuses, one for each model. Loop through them and make sure all are done.
        waiting = any(map(lambda x: 'Queued' == x.details.status or 'InProgress' == x.details.status, info))
        if waiting:
            print ("Waiting 10 seconds for training to complete...")
            time.sleep(10)
# </train>

# <publish>
def publish_app(app_id, app_version):
    responseEndpointInfo = client.apps.publish(app_id, app_version, is_staging=True)
    print("Application published. Endpoint URL: " + responseEndpointInfo.endpoint_url)
# </publish>

# <predict>
def predict(app_id, publishInfo, slot_name):

    request = { "query" : "Find flight to seattle" }

    # Note be sure to specify, using the slot_name parameter, whether your application is in staging or production.
    response = clientRuntime.prediction.get_slot_prediction(app_id=app_id, slot_name=slot_name, prediction_request=request)

    print("Top intent: {}".format(response.prediction.top_intent))
    print("Sentiment: {}".format (response.prediction.sentiment))
    print("Intents: ")

    for intent in response.prediction.intents:
        print("\t{}".format (json.dumps (intent)))
    print("Entities: {}".format (response.prediction.entities))
# </predict>

print("Creating application...")
app_id, app_version = create_app()
print()

print ("Adding entities to application...")
add_entities(app_id, app_version)
print ()

print ("Adding intents to application...")
add_intents(app_id, app_version)
print ()

print ("Adding utterances to application...")
add_utterances(app_id, app_version)
print ()

print ("Training application...")
train_app(app_id, app_version)
print ()

print ("Publishing application...")
publish_app(app_id, app_version)
```

## <a name="add-example-utterance-to-intent"></a>将示例言语添加到意向

为了确定言语的意向并提取实体，应用需要言语示例。 这些示例需要针对特定的单个意向，并且应该标记所有自定义实体。 无需标记预生成实体。

通过创建 [ExampleLabelObject](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python) 对象的列表来添加示例言语（每个示例言语对应于一个对象）。 每个示例应使用实体名称和实体值的名称/值对字典来标记所有实体。 实体值应与示例言语文本中显示的值完全相同。

结合应用 ID、版本 ID 和示例列表调用 [examples.batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-)。 该调用将以结果列表做出响应。 需要检查每个示例的结果，以确保该示例已成功添加到模型中。

```python
# Azure Language Understanding (LUIS) - Build App
#
# This script builds a LUIS app, entities, and intents using the Python
# LUIS SDK.  A separate sample trains and publishes the app.
#
# This script requires the Cognitive Services LUIS Python module:
#     python -m pip install azure-cognitiveservices-language-luis
#
# This script runs under Python 3.4 or later.

# Be sure you understand how LUIS models work.  In particular, know what
# intents, entities, and utterances are, and how they work together in the
# context of a LUIS app. See the following:
#
# https://luis.azure.cn/welcome
# /cognitive-services/luis/luis-concept-intent
# /cognitive-services/luis/luis-concept-entity-types
# /cognitive-services/luis/luis-concept-utterance

# <Dependencies>
from azure.cognitiveservices.language.luis.authoring import LUISAuthoringClient
from msrest.authentication import CognitiveServicesCredentials

import datetime, json, os, time
# </Dependencies>

# <AuthorizationVariables>
authoring_key = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY'

authoring_endpoint = "https://REPLACE-WITH-RESOURCE-NAME.cognitiveservices.azure.cn/"
# </AuthorizationVariables>

# <Client>
# Instantiate a LUIS client
client = LUISAuthoringClient(authoring_endpoint, CognitiveServicesCredentials(authoring_key))
# </Client>

# <createApp>
def create_app():
    # Create a new LUIS app
    app_name    = "Contoso {}".format(datetime.datetime.now())
    app_desc    = "Flight booking app built with LUIS Python SDK."
    app_version = "0.1"
    app_locale  = "en-us"

    app_id = client.apps.add(dict(name=app_name,
                                    initial_version_id=app_version,
                                    description=app_desc,
                                    culture=app_locale))

    print("Created LUIS app {}\n    with ID {}".format(app_name, app_id))
    return app_id, app_version
# </createApp>

# Declare entities:
#
#   Destination - A simple entity that will hold the flight destination
#
#   Class - A hierarchical entity that will hold the flight class
#           (First, Business, or Economy)
#
#   Flight - A composite entity represeting the flight (including
#               class and destination)
#
# Creating an entity (or other LUIS object) returns its ID.
# We don't use IDs further in this script, so we don't keep the return value.
# <addEntities>
def add_entities(app_id, app_version):

    destinationEntityId = client.model.add_entity(app_id, app_version, name="Destination")
    print("destinationEntityId {} added.".format(destinationEntityId))

    classEntityId = client.model.add_entity(app_id, app_version, name="Class")
    print("classEntityId {} added.".format(classEntityId))

    flightEntityId = client.model.add_entity(app_id, app_version, name="Flight")
    print("flightEntityId {} added.".format(flightEntityId))

# </addEntities>

# Declare an intent, FindFlights, that recognizes a user's Flight request
# Creating an intent returns its ID, which we don't need, so don't keep.
# <addIntents>
def add_intents(app_id, app_version):
    intentId = client.model.add_intent(app_id, app_version, "FindFlights")

    print("Intent FindFlights {} added.".format(intentId))
# </addIntents>


# Helper function for creating the utterance data structure.
# <createUtterance>
def create_utterance(intent, utterance, *labels):
    """Add an example LUIS utterance from utterance text and a list of
       labels.  Each label is a 2-tuple containing a label name and the
       text within the utterance that represents that label.

       Utterances apply to a specific intent, which must be specified."""

    text = utterance.lower()

    def label(name, value):
        value = value.lower()
        start = text.index(value)
        return dict(entity_name=name, start_char_index=start,
                    end_char_index=start + len(value))

    return dict(text=text, intent_name=intent,
                entity_labels=[label(n, v) for (n, v) in labels])
# </createUtterance>

# Add example utterances for the intent.  Each utterance includes labels
# that identify the entities within each utterance by index.  LUIS learns
# how to find entities within user utterances from the provided examples.
#
# Example utterance: "find flights in economy to Madrid"
# Labels: Flight -> "economy to Madrid" (composite of Destination and Class)
#         Destination -> "Madrid"
#         Class -> "economy"
# <addUtterances>
def add_utterances(app_id, app_version):
    # Now define the utterances
    utterances = [create_utterance("FindFlights", "find flights in economy to Madrid",
                            ("Flight", "economy to Madrid"),
                            ("Destination", "Madrid"),
                            ("Class", "economy")),

                  create_utterance("FindFlights", "find flights to London in first class",
                            ("Flight", "London in first class"),
                            ("Destination", "London"),
                            ("Class", "first")),

                  create_utterance("FindFlights", "find flights from seattle to London in first class",
                            ("Flight", "flights from seattle to London in first class"),
                            ("Destination", "London"),
                            ("Class", "first"))]

    # Add the utterances in batch. You may add any number of example utterances
    # for any number of intents in one call.
    client.examples.batch(app_id, app_version, utterances)
    print("{} example utterance(s) added.".format(len(utterances)))
# </addUtterances>

# <train>
def train_app(app_id, app_version):
    response = client.train.train_version(app_id, app_version)
    waiting = True
    while waiting:
        info = client.train.get_status(app_id, app_version)

        # get_status returns a list of training statuses, one for each model. Loop through them and make sure all are done.
        waiting = any(map(lambda x: 'Queued' == x.details.status or 'InProgress' == x.details.status, info))
        if waiting:
            print ("Waiting 10 seconds for training to complete...")
            time.sleep(10)
# </train>

# <publish>
def publish_app(app_id, app_version):
    responseEndpointInfo = client.apps.publish(app_id, app_version, is_staging=True)
    print("Application published. Endpoint URL: " + responseEndpointInfo.endpoint_url)
# </publish>

# <predict>
def predict(app_id, publishInfo, slot_name):

    request = { "query" : "Find flight to seattle" }

    # Note be sure to specify, using the slot_name parameter, whether your application is in staging or production.
    response = clientRuntime.prediction.get_slot_prediction(app_id=app_id, slot_name=slot_name, prediction_request=request)

    print("Top intent: {}".format(response.prediction.top_intent))
    print("Sentiment: {}".format (response.prediction.sentiment))
    print("Intents: ")

    for intent in response.prediction.intents:
        print("\t{}".format (json.dumps (intent)))
    print("Entities: {}".format (response.prediction.entities))
# </predict>

print("Creating application...")
app_id, app_version = create_app()
print()

print ("Adding entities to application...")
add_entities(app_id, app_version)
print ()

print ("Adding intents to application...")
add_intents(app_id, app_version)
print ()

print ("Adding utterances to application...")
add_utterances(app_id, app_version)
print ()

print ("Training application...")
train_app(app_id, app_version)
print ()

print ("Publishing application...")
publish_app(app_id, app_version)
```

## <a name="train-the-app"></a>训练应用

创建模型后，需要为此模型版本训练 LUIS 应用。 训练后的模型可在[容器](../luis-container-howto.md)中使用，或者将其[发布](../luis-how-to-publish-app.md)到过渡槽或生产槽。

[train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) 方法需要应用 ID 和版本 ID。

极小的模型（如本快速入门中所示的模型）很快就能完成训练。 对于生产级应用程序，应用的训练应该包括轮询调用 [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) 方法以确定训练何时成功或者是否成功。 响应是一个 [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) 对象列表，其中分别列出了每个对象的状态。 所有对象必须成功，才能将训练视为完成。

```python
# Azure Language Understanding (LUIS) - Build App
#
# This script builds a LUIS app, entities, and intents using the Python
# LUIS SDK.  A separate sample trains and publishes the app.
#
# This script requires the Cognitive Services LUIS Python module:
#     python -m pip install azure-cognitiveservices-language-luis
#
# This script runs under Python 3.4 or later.

# Be sure you understand how LUIS models work.  In particular, know what
# intents, entities, and utterances are, and how they work together in the
# context of a LUIS app. See the following:
#
# https://luis.azure.cn/welcome
# /cognitive-services/luis/luis-concept-intent
# /cognitive-services/luis/luis-concept-entity-types
# /cognitive-services/luis/luis-concept-utterance

# <Dependencies>
from azure.cognitiveservices.language.luis.authoring import LUISAuthoringClient
from msrest.authentication import CognitiveServicesCredentials

import datetime, json, os, time
# </Dependencies>

# <AuthorizationVariables>
authoring_key = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY'

authoring_endpoint = "https://REPLACE-WITH-RESOURCE-NAME.cognitiveservices.azure.cn/"
# </AuthorizationVariables>

# <Client>
# Instantiate a LUIS client
client = LUISAuthoringClient(authoring_endpoint, CognitiveServicesCredentials(authoring_key))
# </Client>

# <createApp>
def create_app():
    # Create a new LUIS app
    app_name    = "Contoso {}".format(datetime.datetime.now())
    app_desc    = "Flight booking app built with LUIS Python SDK."
    app_version = "0.1"
    app_locale  = "en-us"

    app_id = client.apps.add(dict(name=app_name,
                                    initial_version_id=app_version,
                                    description=app_desc,
                                    culture=app_locale))

    print("Created LUIS app {}\n    with ID {}".format(app_name, app_id))
    return app_id, app_version
# </createApp>

# Declare entities:
#
#   Destination - A simple entity that will hold the flight destination
#
#   Class - A hierarchical entity that will hold the flight class
#           (First, Business, or Economy)
#
#   Flight - A composite entity represeting the flight (including
#               class and destination)
#
# Creating an entity (or other LUIS object) returns its ID.
# We don't use IDs further in this script, so we don't keep the return value.
# <addEntities>
def add_entities(app_id, app_version):

    destinationEntityId = client.model.add_entity(app_id, app_version, name="Destination")
    print("destinationEntityId {} added.".format(destinationEntityId))

    classEntityId = client.model.add_entity(app_id, app_version, name="Class")
    print("classEntityId {} added.".format(classEntityId))

    flightEntityId = client.model.add_entity(app_id, app_version, name="Flight")
    print("flightEntityId {} added.".format(flightEntityId))

# </addEntities>

# Declare an intent, FindFlights, that recognizes a user's Flight request
# Creating an intent returns its ID, which we don't need, so don't keep.
# <addIntents>
def add_intents(app_id, app_version):
    intentId = client.model.add_intent(app_id, app_version, "FindFlights")

    print("Intent FindFlights {} added.".format(intentId))
# </addIntents>


# Helper function for creating the utterance data structure.
# <createUtterance>
def create_utterance(intent, utterance, *labels):
    """Add an example LUIS utterance from utterance text and a list of
       labels.  Each label is a 2-tuple containing a label name and the
       text within the utterance that represents that label.

       Utterances apply to a specific intent, which must be specified."""

    text = utterance.lower()

    def label(name, value):
        value = value.lower()
        start = text.index(value)
        return dict(entity_name=name, start_char_index=start,
                    end_char_index=start + len(value))

    return dict(text=text, intent_name=intent,
                entity_labels=[label(n, v) for (n, v) in labels])
# </createUtterance>

# Add example utterances for the intent.  Each utterance includes labels
# that identify the entities within each utterance by index.  LUIS learns
# how to find entities within user utterances from the provided examples.
#
# Example utterance: "find flights in economy to Madrid"
# Labels: Flight -> "economy to Madrid" (composite of Destination and Class)
#         Destination -> "Madrid"
#         Class -> "economy"
# <addUtterances>
def add_utterances(app_id, app_version):
    # Now define the utterances
    utterances = [create_utterance("FindFlights", "find flights in economy to Madrid",
                            ("Flight", "economy to Madrid"),
                            ("Destination", "Madrid"),
                            ("Class", "economy")),

                  create_utterance("FindFlights", "find flights to London in first class",
                            ("Flight", "London in first class"),
                            ("Destination", "London"),
                            ("Class", "first")),

                  create_utterance("FindFlights", "find flights from seattle to London in first class",
                            ("Flight", "flights from seattle to London in first class"),
                            ("Destination", "London"),
                            ("Class", "first"))]

    # Add the utterances in batch. You may add any number of example utterances
    # for any number of intents in one call.
    client.examples.batch(app_id, app_version, utterances)
    print("{} example utterance(s) added.".format(len(utterances)))
# </addUtterances>

# <train>
def train_app(app_id, app_version):
    response = client.train.train_version(app_id, app_version)
    waiting = True
    while waiting:
        info = client.train.get_status(app_id, app_version)

        # get_status returns a list of training statuses, one for each model. Loop through them and make sure all are done.
        waiting = any(map(lambda x: 'Queued' == x.details.status or 'InProgress' == x.details.status, info))
        if waiting:
            print ("Waiting 10 seconds for training to complete...")
            time.sleep(10)
# </train>

# <publish>
def publish_app(app_id, app_version):
    responseEndpointInfo = client.apps.publish(app_id, app_version, is_staging=True)
    print("Application published. Endpoint URL: " + responseEndpointInfo.endpoint_url)
# </publish>

# <predict>
def predict(app_id, publishInfo, slot_name):

    request = { "query" : "Find flight to seattle" }

    # Note be sure to specify, using the slot_name parameter, whether your application is in staging or production.
    response = clientRuntime.prediction.get_slot_prediction(app_id=app_id, slot_name=slot_name, prediction_request=request)

    print("Top intent: {}".format(response.prediction.top_intent))
    print("Sentiment: {}".format (response.prediction.sentiment))
    print("Intents: ")

    for intent in response.prediction.intents:
        print("\t{}".format (json.dumps (intent)))
    print("Entities: {}".format (response.prediction.entities))
# </predict>

print("Creating application...")
app_id, app_version = create_app()
print()

print ("Adding entities to application...")
add_entities(app_id, app_version)
print ()

print ("Adding intents to application...")
add_intents(app_id, app_version)
print ()

print ("Adding utterances to application...")
add_utterances(app_id, app_version)
print ()

print ("Training application...")
train_app(app_id, app_version)
print ()

print ("Publishing application...")
publish_app(app_id, app_version)
```

## <a name="publish-a-language-understanding-app"></a>发布语言理解应用

使用 [app.publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) 方法发布 LUIS 应用。 这会将当前已训练的版本发布到终结点上的指定槽。 客户端应用程序使用此终结点发送用户言语，以预测意向和提取实体。

```python
# Azure Language Understanding (LUIS) - Build App
#
# This script builds a LUIS app, entities, and intents using the Python
# LUIS SDK.  A separate sample trains and publishes the app.
#
# This script requires the Cognitive Services LUIS Python module:
#     python -m pip install azure-cognitiveservices-language-luis
#
# This script runs under Python 3.4 or later.

# Be sure you understand how LUIS models work.  In particular, know what
# intents, entities, and utterances are, and how they work together in the
# context of a LUIS app. See the following:
#
# https://luis.azure.cn/welcome
# /cognitive-services/luis/luis-concept-intent
# /cognitive-services/luis/luis-concept-entity-types
# /cognitive-services/luis/luis-concept-utterance

# <Dependencies>
from azure.cognitiveservices.language.luis.authoring import LUISAuthoringClient
from msrest.authentication import CognitiveServicesCredentials

import datetime, json, os, time
# </Dependencies>

# <AuthorizationVariables>
authoring_key = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY'

authoring_endpoint = "https://REPLACE-WITH-RESOURCE-NAME.cognitiveservices.azure.cn/"
# </AuthorizationVariables>

# <Client>
# Instantiate a LUIS client
client = LUISAuthoringClient(authoring_endpoint, CognitiveServicesCredentials(authoring_key))
# </Client>

# <createApp>
def create_app():
    # Create a new LUIS app
    app_name    = "Contoso {}".format(datetime.datetime.now())
    app_desc    = "Flight booking app built with LUIS Python SDK."
    app_version = "0.1"
    app_locale  = "en-us"

    app_id = client.apps.add(dict(name=app_name,
                                    initial_version_id=app_version,
                                    description=app_desc,
                                    culture=app_locale))

    print("Created LUIS app {}\n    with ID {}".format(app_name, app_id))
    return app_id, app_version
# </createApp>

# Declare entities:
#
#   Destination - A simple entity that will hold the flight destination
#
#   Class - A hierarchical entity that will hold the flight class
#           (First, Business, or Economy)
#
#   Flight - A composite entity represeting the flight (including
#               class and destination)
#
# Creating an entity (or other LUIS object) returns its ID.
# We don't use IDs further in this script, so we don't keep the return value.
# <addEntities>
def add_entities(app_id, app_version):

    destinationEntityId = client.model.add_entity(app_id, app_version, name="Destination")
    print("destinationEntityId {} added.".format(destinationEntityId))

    classEntityId = client.model.add_entity(app_id, app_version, name="Class")
    print("classEntityId {} added.".format(classEntityId))

    flightEntityId = client.model.add_entity(app_id, app_version, name="Flight")
    print("flightEntityId {} added.".format(flightEntityId))

# </addEntities>

# Declare an intent, FindFlights, that recognizes a user's Flight request
# Creating an intent returns its ID, which we don't need, so don't keep.
# <addIntents>
def add_intents(app_id, app_version):
    intentId = client.model.add_intent(app_id, app_version, "FindFlights")

    print("Intent FindFlights {} added.".format(intentId))
# </addIntents>


# Helper function for creating the utterance data structure.
# <createUtterance>
def create_utterance(intent, utterance, *labels):
    """Add an example LUIS utterance from utterance text and a list of
       labels.  Each label is a 2-tuple containing a label name and the
       text within the utterance that represents that label.

       Utterances apply to a specific intent, which must be specified."""

    text = utterance.lower()

    def label(name, value):
        value = value.lower()
        start = text.index(value)
        return dict(entity_name=name, start_char_index=start,
                    end_char_index=start + len(value))

    return dict(text=text, intent_name=intent,
                entity_labels=[label(n, v) for (n, v) in labels])
# </createUtterance>

# Add example utterances for the intent.  Each utterance includes labels
# that identify the entities within each utterance by index.  LUIS learns
# how to find entities within user utterances from the provided examples.
#
# Example utterance: "find flights in economy to Madrid"
# Labels: Flight -> "economy to Madrid" (composite of Destination and Class)
#         Destination -> "Madrid"
#         Class -> "economy"
# <addUtterances>
def add_utterances(app_id, app_version):
    # Now define the utterances
    utterances = [create_utterance("FindFlights", "find flights in economy to Madrid",
                            ("Flight", "economy to Madrid"),
                            ("Destination", "Madrid"),
                            ("Class", "economy")),

                  create_utterance("FindFlights", "find flights to London in first class",
                            ("Flight", "London in first class"),
                            ("Destination", "London"),
                            ("Class", "first")),

                  create_utterance("FindFlights", "find flights from seattle to London in first class",
                            ("Flight", "flights from seattle to London in first class"),
                            ("Destination", "London"),
                            ("Class", "first"))]

    # Add the utterances in batch. You may add any number of example utterances
    # for any number of intents in one call.
    client.examples.batch(app_id, app_version, utterances)
    print("{} example utterance(s) added.".format(len(utterances)))
# </addUtterances>

# <train>
def train_app(app_id, app_version):
    response = client.train.train_version(app_id, app_version)
    waiting = True
    while waiting:
        info = client.train.get_status(app_id, app_version)

        # get_status returns a list of training statuses, one for each model. Loop through them and make sure all are done.
        waiting = any(map(lambda x: 'Queued' == x.details.status or 'InProgress' == x.details.status, info))
        if waiting:
            print ("Waiting 10 seconds for training to complete...")
            time.sleep(10)
# </train>

# <publish>
def publish_app(app_id, app_version):
    responseEndpointInfo = client.apps.publish(app_id, app_version, is_staging=True)
    print("Application published. Endpoint URL: " + responseEndpointInfo.endpoint_url)
# </publish>

# <predict>
def predict(app_id, publishInfo, slot_name):

    request = { "query" : "Find flight to seattle" }

    # Note be sure to specify, using the slot_name parameter, whether your application is in staging or production.
    response = clientRuntime.prediction.get_slot_prediction(app_id=app_id, slot_name=slot_name, prediction_request=request)

    print("Top intent: {}".format(response.prediction.top_intent))
    print("Sentiment: {}".format (response.prediction.sentiment))
    print("Intents: ")

    for intent in response.prediction.intents:
        print("\t{}".format (json.dumps (intent)))
    print("Entities: {}".format (response.prediction.entities))
# </predict>

print("Creating application...")
app_id, app_version = create_app()
print()

print ("Adding entities to application...")
add_entities(app_id, app_version)
print ()

print ("Adding intents to application...")
add_intents(app_id, app_version)
print ()

print ("Adding utterances to application...")
add_utterances(app_id, app_version)
print ()

print ("Training application...")
train_app(app_id, app_version)
print ()

print ("Publishing application...")
publish_app(app_id, app_version)
```

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `python` 命令运行应用程序。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>清理资源

完成预测后，请删除文件及其子目录，对此快速入门中的工作进行清理。

