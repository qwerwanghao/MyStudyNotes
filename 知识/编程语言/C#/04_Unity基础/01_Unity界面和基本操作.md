---
title: Unity界面和基本操作
type: knowledge
date: '2026-04-12'
status: distilled
tags:
- 知识
- 编程语言
- C#
- Unity基础
source: []
related:
- Unity游戏开发基础
- C# 编程语言学习笔记
- 游戏对象和组件
---
# Unity界面和基本操作

## 主要界面区域

### Scene视图
```plaintext
1. 视图控制
   - 右键拖拽：旋转视图
   - 中键拖拽：平移视图
   - 滚轮：缩放视图
   - Q/W/E/R：选择/移动/旋转/缩放工具
   - F：聚焦选中物体
   - Ctrl+Alt+左键拖拽：环绕视角

2. 场景设置
   - 2D/3D切换
   - 网格显示控制
   - 光照渲染模式
   - 场景导航
```

### Game视图
```plaintext
1. 分辨率设置
   - 自定义分辨率
   - 屏幕比例
   - 设备模拟器

2. 播放控制
   - 播放按钮
   - 暂停按钮
   - 逐帧播放
```

### Hierarchy窗口
```plaintext
1. 层级管理
   - 创建游戏对象
   - 组织层级关系
   - 查找和筛选
   - 多选操作

2. 快捷操作
   - F2：重命名
   - Ctrl+D：复制
   - Delete：删除
   - Ctrl+Shift+N：创建空物体
```

### Project窗口
```plaintext
1. 资源管理
   - 文件夹组织
   - 资源导入
   - 资源预览
   - 快速搜索

2. 常用操作
   - 创建资源
   - 重命名资源
   - 移动资源
   - 删除资源
```

### Inspector窗口
```plaintext
1. 组件操作
   - 添加组件
   - 修改属性
   - 复制组件
   - 禁用组件

2. 预览功能
   - 材质预览
   - 动画预览
   - 音频预览
```

## 基本操作技巧

### 对象操作
```csharp
// 创建对象
GameObject cube = GameObject.CreatePrimitive(PrimitiveType.Cube);
cube.transform.position = new Vector3(0, 1, 0);

// 选择对象
Selection.activeGameObject = cube;

// 移动对象
cube.transform.Translate(Vector3.forward * Time.deltaTime);

// 旋转对象
cube.transform.Rotate(Vector3.up * 45f);

// 缩放对象
cube.transform.localScale = new Vector3(2, 2, 2);
```

### 场景导航
```csharp
// 相机控制
public class CameraController : MonoBehaviour
{
    public float moveSpeed = 10f;
    public float rotateSpeed = 100f;
    
    private void Update()
    {
        // WASD移动
        float horizontal = Input.GetAxis("Horizontal");
        float vertical = Input.GetAxis("Vertical");
        transform.Translate(new Vector3(horizontal, 0, vertical) 
            * moveSpeed * Time.deltaTime);
        
        // 鼠标右键旋转
        if (Input.GetMouseButton(1))
        {
            float mouseX = Input.GetAxis("Mouse X");
            float mouseY = Input.GetAxis("Mouse Y");
            transform.RotateAround(transform.position, Vector3.up, 
                mouseX * rotateSpeed * Time.deltaTime);
            transform.RotateAround(transform.position, transform.right, 
                -mouseY * rotateSpeed * Time.deltaTime);
        }
    }
}
```

### 快捷键系统
```plaintext
1. 编辑快捷键
   - Ctrl+Z：撤销
   - Ctrl+Y：重做
   - Ctrl+S：保存
   - Ctrl+C/V：复制/粘贴
   - Alt+拖拽：复制对象

2. 视图快捷键
   - Scene：移动/旋转/缩放（W/E/R）
   - F：聚焦选中物体
   - Alt+左键：环绕视角
   - Ctrl+1/2/3/4：切换布局

3. 窗口快捷键
   - Ctrl+Shift+C：Console窗口
   - Ctrl+P：Play模式
   - Ctrl+Shift+B：构建设置
```

## 编辑器扩展

### 自定义工具
```csharp
public class EditorTools : EditorWindow
{
    [MenuItem("Tools/My Custom Window")]
    public static void ShowWindow()
    {
        GetWindow<EditorTools>("My Tools");
    }
    
    private void OnGUI()
    {
        // 添加自定义工具按钮
        if (GUILayout.Button("Clear Console"))
        {
            var logEntries = System.Type.GetType("UnityEditor.LogEntries, UnityEditor.dll");
            var clearMethod = logEntries.GetMethod("Clear");
            clearMethod.Invoke(new object(), null);
        }
    }
}
```

### 自定义Inspector
```csharp
[CustomEditor(typeof(MyComponent))]
public class MyComponentEditor : Editor
{
    public override void OnInspectorGUI()
    {
        MyComponent myTarget = (MyComponent)target;
        
        // 添加自定义字段
        myTarget.customValue = EditorGUILayout.FloatField(
            "Custom Value", myTarget.customValue);
        
        // 添加自定义按钮
        if (GUILayout.Button("Do Something"))
        {
            myTarget.DoSomething();
        }
    }
}
```

## 项目设置

### 基本配置
```plaintext
1. Player Settings
   - 公司和产品名称
   - 版本号
   - 目标平台
   - 图标设置

2. Quality Settings
   - 图形质量等级
   - 纹理质量
   - 抗锯齿
   - 阴影设置

3. Physics Settings
   - 重力设置
   - 碰撞层设置
   - 射线检测设置
```

### 输入设置
```plaintext
1. Input Manager
   - 按键映射
   - 轴向设置
   - 手柄支持
   - 触摸输入

2. 新输入系统
   - Input Actions
   - Input Controls
   - Input Bindings
   - Input Processors

## 相关笔记
<!-- AUTO-RELATED-START -->
- [Unity游戏开发基础](04_Unity基础.md)
- [C# 编程语言学习笔记](<../C#.md>)
- [游戏对象和组件](02_游戏对象和组件.md)
<!-- AUTO-RELATED-END -->
