---
title: 教程：使用 Android SDK 检测和定格图像中的人脸
titleSuffix: Azure Cognitive Services
description: 在本教程中，你将创建一个简单的 Android 应用，它使用人脸 API 来检测和定格图像中的人脸。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
origin.date: 07/03/2019
ms.date: 12/01/2020
ms.author: v-junlch
ms.openlocfilehash: 5aae53157eb65070279c3b33dfeb9d6eb1285a9e
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432433"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>教程：创建一个用于检测和定格图像中人脸的 Android 应用

在本教程中，你将创建一个简单的 Android 应用程序，它使用 Azure 人脸 API 通过 Java SDK 来检测图像中的人脸。 此应用程序显示一个选定的图像，然后围绕每个检测到的人脸绘制一个框。

本教程演示如何：

> [!div class="checklist"]
> - 创建 Android 应用程序
> - 安装人脸 API 客户端库
> - 使用客户端库检测图像中的人脸
> - 围绕每个检测到的人脸绘制一个框架

![一张照片的 Android 屏幕截图，其中的人脸已使用红色矩形定格](../Images/android_getstarted2.1.PNG)

完整的示例代码在 GitHub 上的[认知服务人脸 Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) 存储库中提供。

如果没有 Azure 订阅，请在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。 

## <a name="prerequisites"></a>必备条件

- 人脸 API 订阅密钥。 可以按照[创建认知服务帐户](/cognitive-services/cognitive-services-apis-create-account)中的说明订阅人脸 API 服务并获取密钥。
- [Android Studio](https://developer.android.com/studio/)，装有 API 22 或更高级别（这是人脸客户端库需要的）。

## <a name="create-the-android-studio-project"></a>创建 Android Studio 项目

执行以下步骤，以便创建新的 Android 应用程序项目。

1. 在 Android Studio 中，选择“启动新的 Android Studio 项目”。 
1. 在“创建 Android 项目”屏幕上根据需要修改默认字段，然后单击“下一步”。  
1. 在“目标 Android 设备”屏幕上使用下拉列表选择器选择“API 22”或更高版本，然后单击“下一步”。   
1. 选择“空活动”，然后单击“下一步”。  
1. 取消选中“后向兼容性”，然后单击“完成”。  

## <a name="add-the-initial-code"></a>添加初始代码

### <a name="create-the-ui"></a>创建 UI

打开 *activity_main.xml*。 在布局编辑器中，选择“文本”选项卡，然后将其中的内容替换为以下代码。 

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" tools:context=".MainActivity"
    android:layout_width="match_parent" android:layout_height="match_parent">

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="fill_parent"
        android:id="@+id/imageView1"
        android:layout_above="@+id/button1"
        android:contentDescription="Image with faces to analyze"/>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Browse for a face image"
        android:id="@+id/button1"
        android:layout_alignParentBottom="true"/>
</RelativeLayout>
```

### <a name="create-the-main-class"></a>创建主类

打开 *MainActivity.java*，将现有 `import` 语句替换为以下代码。

```java
import java.io.*;
import android.app.*;
import android.content.*;
import android.net.*;
import android.os.*;
import android.view.*;
import android.graphics.*;
import android.widget.*;
import android.provider.*;
```

然后，将 **MainActivity** 类的内容替换为以下代码。 这样会在“按钮”  上设置一个事件处理程序，以便启动一项让用户选择图片的新活动。 它会在 **ImageView** 中显示图片。

```java
private final int PICK_IMAGE = 1;
private ProgressDialog detectionProgressDialog;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    Button button1 = findViewById(R.id.button1);
    button1.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
            intent.setType("image/*");
            startActivityForResult(Intent.createChooser(
                    intent, "Select Picture"), PICK_IMAGE);
        }
    });

    detectionProgressDialog = new ProgressDialog(this);
}

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
            data != null && data.getData() != null) {
        Uri uri = data.getData();
        try {
            Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                    getContentResolver(), uri);
            ImageView imageView = findViewById(R.id.imageView1);
            imageView.setImageBitmap(bitmap);

            // Comment out for tutorial
            detectAndFrame(bitmap);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### <a name="try-the-app"></a>试用应用

在 **onActivityResult** 方法中注释掉对 **detectAndFrame** 的调用。 然后按菜单上的“运行”，对应用进行测试。  当应用打开后，在模拟器或连接的设备中单击底部的“浏览”。  此时会显示设备的文件选择对话框。 选择一个图像，验证它是否显示在窗口中。 然后，关闭应用并转到下一步。

![一张包含人脸的照片的 Android 屏幕截图](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>添加人脸 SDK

### <a name="add-the-gradle-dependency"></a>添加 Gradle 依赖项

在“项目”窗格中，使用下拉列表选择器选择“Android”。   展开“Gradle 脚本”，  然后打开“build.gradle (模块: 应用)”。  为人脸客户端库 `com.microsoft.projectoxford:face:1.4.3` 添加一个依赖项，如以下屏幕截图所示，然后单击“立即同步”。 

![应用 build.gradle 文件的 Android Studio 屏幕截图](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>添加与人脸相关的项目代码

返回到 **MainActivity.java**，添加以下 `import` 语句：

```java
import com.microsoft.projectoxford.face.*;
import com.microsoft.projectoxford.face.contract.*;
```

然后，在 **MainActivity** 类中 **onCreate** 方法上面插入以下代码：

```java
private final String apiEndpoint = "https://api.cognitive.azure.cn/face/v1.0"

// Replace `<Subscription Key>` with your subscription key.
// For example, subscriptionKey = "0123456789abcdef0123456789ABCDEF"
private final String subscriptionKey = "<Subscription Key>";

private final FaceServiceClient faceServiceClient =
        new FaceServiceRestClient(apiEndpoint, subscriptionKey);
```

> [!NOTE]
> 如果创建的人脸 API 的 `Location` 为 `China East 2`，则需要将 URL `https://api.cognitive.azure.cn/face/v1.0` 替换为 `https://chinaeast2.api.cognitive.azure.cn/face/v1.0`。

需将 `<Subscription Key>` 替换为订阅密钥。 另外，请使用适合密钥的区域标识符将 `<API endpoint>` 替换为人脸 API 终结点（如需包含所有区域终结点的列表，请参阅[人脸 API 文档](https://dev.cognitive.azure.cn/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)）。 

在“项目”窗格中  展开“应用”，接着展开“清单”   ，然后打开 *AndroidManifest.xml*。 插入以下元素作为 `manifest` 元素的直接子级：

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="upload-image-and-detect-faces"></a>上传图像并检测人脸

应用会通过调用 **faceClient.Face.DetectWithStreamAsync** 方法来检测人脸，该方法会包装 [检测](https://dev.cognitive.azure.cn/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API 并返回 **人脸** 实例的列表。

返回的每张 **人脸** 均包含一个指示其位置的矩形，以及一系列可选的人脸特性。 在本示例中，只请求了人脸矩形。

将以下两个方法插入到 **MainActivity** 类中。 请注意，当人脸检测完成后，应用会调用 **drawFaceRectanglesOnBitmap** 方法，以便修改 **ImageView**。 接下来将定义此方法。

```java
// Detect faces by uploading a face image.
// Frame faces after detection.
private void detectAndFrame(final Bitmap imageBitmap) {
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
    ByteArrayInputStream inputStream =
            new ByteArrayInputStream(outputStream.toByteArray());

    AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                String exceptionMessage = "";

                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0],
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null          // returnFaceAttributes:
                                /* new FaceServiceClient.FaceAttributeType[] {
                                    FaceServiceClient.FaceAttributeType.Age,
                                    FaceServiceClient.FaceAttributeType.Gender }
                                */
                        );
                        if (result == null){
                            publishProgress(
                                    "Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(String.format(
                                "Detection Finished. %d face(s) detected",
                                result.length));
                        return result;
                    } catch (Exception e) {
                        exceptionMessage = String.format(
                                "Detection failed: %s", e.getMessage());
                        return null;
                    }
                }

                @Override
                protected void onPreExecute() {
                    //TODO: show progress dialog
                    detectionProgressDialog.show();
                }
                @Override
                protected void onProgressUpdate(String... progress) {
                    //TODO: update progress
                    detectionProgressDialog.setMessage(progress[0]);
                }
                @Override
                protected void onPostExecute(Face[] result) {
                    //TODO: update face frames
                    detectionProgressDialog.dismiss();

                    if(!exceptionMessage.equals("")){
                        showError(exceptionMessage);
                    }
                    if (result == null) return;

                    ImageView imageView = findViewById(R.id.imageView1);
                    imageView.setImageBitmap(
                            drawFaceRectanglesOnBitmap(imageBitmap, result));
                    imageBitmap.recycle();
                }
            };

    detectTask.execute(inputStream);
}

private void showError(String message) {
    new AlertDialog.Builder(this)
            .setTitle("Error")
            .setMessage(message)
            .setPositiveButton("OK", new DialogInterface.OnClickListener() {
                public void onClick(DialogInterface dialog, int id) {
                }})
            .create().show();
}
```

## <a name="draw-face-rectangles"></a>绘制人脸矩形

将以下帮助程序方法插入到 **MainActivity** 类中。 此方法围绕每个检测到的人脸绘制一个矩形，使用每个 **人脸** 实例的矩形坐标。

```java
private static Bitmap drawFaceRectanglesOnBitmap(
        Bitmap originalBitmap, Face[] faces) {
    Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
    Canvas canvas = new Canvas(bitmap);
    Paint paint = new Paint();
    paint.setAntiAlias(true);
    paint.setStyle(Paint.Style.STROKE);
    paint.setColor(Color.RED);
    paint.setStrokeWidth(10);
    if (faces != null) {
        for (Face face : faces) {
            FaceRectangle faceRectangle = face.faceRectangle;
            canvas.drawRect(
                    faceRectangle.left,
                    faceRectangle.top,
                    faceRectangle.left + faceRectangle.width,
                    faceRectangle.top + faceRectangle.height,
                    paint);
        }
    }
    return bitmap;
}
```

最后，在 **onActivityResult** 中取消注释对 **detectAndFrame** 方法的调用。

## <a name="run-the-app"></a>运行应用

运行此应用程序，以浏览方式查找包含人脸的图像。 等待几秒钟，以便人脸服务响应。 此时会在图像中的每个人脸上看到一个红色矩形。

![人脸的 Android 屏幕截图，围绕这些人脸绘制了红色矩形](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>后续步骤

本教程介绍了人脸 API Java SDK 的基本使用过程，并创建了一个应用程序来检测并定格图像中的人脸。 接下来，请深入了解人脸检测的详细信息。

> [!div class="nextstepaction"]
> [如何检测图像中的人脸](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)

<!-- Update_Description: wording update -->