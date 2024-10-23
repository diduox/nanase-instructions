##### 改变Android虚拟机保存位置

### 步骤：更改 AVD 存储位置

1. **打开 Android Virtual Device Manager**

   - 打开 Android Studio。
   - 点击右上角的 **Device Manager** 图标（或通过菜单栏 `Tools > Device Manager` 打开）。

2. **创建或选择已有的虚拟机**

   - 如果还未创建虚拟机，点击 **Create Device** 按照向导创建虚拟机。
   - 如果已经有虚拟机，点击右侧的三点菜单图标，然后选择 **Show on Disk**。

3. **找到 AVD 的配置文件**

   - AVD 的配置文件通常位于以下路径：
     - **Windows**: `C:\Users\<用户名>\.android\avd\`
     - **macOS**: `/Users/<用户名>/.android/avd/`
     - **Linux**: `/home/<用户名>/.android/avd/`
   - 在这个文件夹中，你会看到一个以 AVD 名称命名的文件夹，如 `Pixel_3_API_30.avd`，这个文件夹中包含虚拟机的所有数据。

4. **修改虚拟机的路径**

   如果想更改虚拟机的数据存储路径，请按照以下步骤操作：

   - **移动 AVD 文件夹**：将 `.avd` 文件夹（如 `Pixel_3_API_30.avd`）移动到你希望存储的磁盘位置，比如 `D:\AndroidAVDs\`。

   - **修改 AVD 配置文件**：

     - 在 `.android\avd` 文件夹中，有一个对应的 `.ini` 文件（例如 `Pixel_3_API_30.ini`）。

     - 用文本编辑器打开该 `.ini` 文件。

     - 找到 `path=` 这一行，将路径改为新的存储地址。例如：

       ```
       ini
       
       
       复制代码
       path=D:\AndroidAVDs\Pixel_3_API_30.avd
       ```

     - 保存并关闭文件。

### 更新环境变量步骤

#### 1. 设置 `ANDROID_AVD_HOME` 环境变量

1. **打开系统环境变量设置**：

   - 在 Windows 搜索栏中输入 **“环境变量”**，选择 **“编辑系统环境变量”**。
   - 在弹出的窗口中，点击 **“环境变量”** 按钮。

2. **新建或更新 `ANDROID_AVD_HOME`**：

   - 在 **“系统变量”** 部分，点击 **“新建”**（如果变量不存在）或 **“编辑”**（如果已经存在）。

   - 设置变量名为 `ANDROID_AVD_HOME`，变量值为 `D:\AndroidAVDs\avd`。

     **变量名**: `ANDROID_AVD_HOME`
     **变量值**: `D:\AndroidAVDs\avd`

3. **保存设置**：

   - 点击 **“确定”** 保存变量设置，然后关闭所有对话框。