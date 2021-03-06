## <a name="view-the-solution-dashboard"></a>查看解决方案仪表板

该解决方案仪表板用于管理部署的解决方案。 例如，可以查看遥测数据、添加设备以及调用方法。

1. 当预配完成且预配置解决方案的磁贴指示“就绪”时，请选择“启动”，在新的选项卡中打开远程监视解决方案门户。

    ![启动预配置解决方案][img-launch-solution]

1. 默认情况下，此解决方案门户会显示*仪表板*。 通过页面左侧的菜单导航到解决方案门户的其他区域。

    ![远程监控预配置解决方案仪表板][img-menu]

## <a name="add-a-device"></a>添加设备

对于连接到预配置解决方案的设备，该设备必须使用有效的凭据将自身标识到 IoT 中心。 可以从解决方案仪表板中检索设备凭据。 在本教程中，稍后会在客户端应用程序中添加设备凭据。

若要在远程监视解决方案中添加设备，请在解决方案仪表板中完成以下步骤：

1. 在仪表板左下角，单击“添加设备”。

   ![添加设备][1]

1. 在“自定义设备”面板中，单击“新增”。

   ![添加自定义设备][2]

1. 选择“让我定义我自己的设备 ID”。 输入设备 ID（例如 **device01**），单击“检查 ID”验证该名称是否尚未在解决方案中使用，并单击“创建”预配设备。

   ![添加设备 ID][3]

1. 记下设备凭据（“设备 ID”、“IoT 中心主机名”和“设备密钥”）。 Intel NUC 上的客户端应用程序需要这些值才能连接到远程监视解决方案。 然后单击“完成”。

    ![查看设备凭据][4]

1. 在解决方案仪表板中的设备列表中选择设备。 接着，在“设备详细信息”面板中，单击“启用设备”。 设备状态现在为“正在运行”。 远程监视解决方案现在可以从设备接收遥测数据，并在设备上调用方法。

[img-launch-solution]: media/iot-suite-v1-gateway-kit-view-solution/launch.png
[img-menu]: media/iot-suite-v1-gateway-kit-view-solution/menu.png
[1]: media/iot-suite-v1-gateway-kit-view-solution/suite0.png
[2]: media/iot-suite-v1-gateway-kit-view-solution/suite1.png
[3]: media/iot-suite-v1-gateway-kit-view-solution/suite2.png
[4]: media/iot-suite-v1-gateway-kit-view-solution/suite3.png