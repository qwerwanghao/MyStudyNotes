---
title: Transform和坐标系统
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
- 预制体系统
- 摄像机系统
- 输入系统
- UI系统
---
# Transform和坐标系统

<!-- DEEP-TOPIC-GUIDE-START -->
> 这个专题是 Unity 空间理解的核心。如果这部分没有真正吃透，后面几乎所有移动、旋转、跟随、摄像机、UI 对齐都会变得混乱。

## 先读这篇时，建议带着这些问题

- 世界坐标和局部坐标到底差在哪
- Transform 为什么是 Unity 里最基础也最容易混淆的组件
- position / localPosition、rotation / localRotation、Scale 分别怎么理解
- 坐标转换为什么会直接影响交互、相机和动画逻辑

## 推荐阅读顺序

1. 坐标系统基础
2. 世界坐标系和局部坐标系
3. 坐标系转换
4. 位置控制
5. 旋转控制
6. 缩放控制

## 这一页最值得重点抓住的概念

- **局部坐标描述的是相对父对象的位置关系**
- **Transform 是所有空间操作的统一入口**
- **坐标转换能力决定你能不能写出稳定的空间逻辑**
- **旋转问题很多时候本质上是坐标系问题**

## 和其他笔记的关系

- 前置理解： [游戏对象和组件](02_游戏对象和组件.md)
- 后续组织对象： [预制体系统](04_预制体系统.md)
- 强相关： [场景管理](05_场景管理.md)
- 相机与移动感知： [摄像机系统](06_摄像机系统.md)
<!-- DEEP-TOPIC-GUIDE-END -->

## 横向关联主题
<!-- CROSS-LINKS-START -->
- [摄像机系统](06_摄像机系统.md)：坐标理解会直接影响镜头跟随、视角切换和观察手感
- [输入系统](07_输入系统.md)：很多输入最终都要被转换成空间移动、朝向或相机控制
- [UI系统](08_UI系统.md)：屏幕空间与世界空间的切换，经常是 UI 与场景交互的关键
<!-- CROSS-LINKS-END -->

## 坐标系统基础

### 世界坐标系和局部坐标系
```csharp
public class CoordinateSystem : MonoBehaviour
{
    private void TransformExample()
    {
        // 世界坐标
        Vector3 worldPosition = transform.position;
        Quaternion worldRotation = transform.rotation;
        
        // 局部坐标
        Vector3 localPosition = transform.localPosition;
        Quaternion localRotation = transform.localRotation;
        
        // 坐标转换
        Vector3 worldPoint = transform.TransformPoint(Vector3.forward);
        Vector3 localPoint = transform.InverseTransformPoint(worldPoint);
    }
}
```

### 坐标系转换
```csharp
public class CoordinateTransform : MonoBehaviour
{
    // 方向转换
    public Vector3 TransformDirection(Vector3 localDirection)
    {
        // 局部方向转世界方向
        Vector3 worldDirection = transform.TransformDirection(localDirection);
        
        // 世界方向转局部方向
        Vector3 backToLocal = transform.InverseTransformDirection(worldDirection);
        return backToLocal;
    }
    
    // 向量转换
    public Vector3 TransformVector(Vector3 localVector)
    {
        // 局部向量转世界向量
        Vector3 worldVector = transform.TransformVector(localVector);
        
        // 世界向量转局部向量
        Vector3 backToLocal = transform.InverseTransformVector(worldVector);
        return backToLocal;
    }
}
```

## Transform操作

### 位置控制
```csharp
public class PositionControl : MonoBehaviour
{
    public float moveSpeed = 5f;
    
    void Update()
    {
        // 直接设置位置
        transform.position = new Vector3(1, 2, 3);
        
        // 平滑移动
        transform.position = Vector3.Lerp(
            transform.position,
            targetPosition,
            Time.deltaTime * moveSpeed
        );
        
        // 使用Translate移动
        transform.Translate(Vector3.forward * moveSpeed * Time.deltaTime);
        
        // 相对于世界空间移动
        transform.Translate(
            Vector3.forward * moveSpeed * Time.deltaTime, 
            Space.World
        );
    }
    
    // MoveTowards示例
    IEnumerator MoveToPoint(Vector3 target)
    {
        while (Vector3.Distance(transform.position, target) > 0.01f)
        {
            transform.position = Vector3.MoveTowards(
                transform.position,
                target,
                moveSpeed * Time.deltaTime
            );
            yield return null;
        }
    }
}
```

### 旋转控制
```csharp
public class RotationControl : MonoBehaviour
{
    public float rotateSpeed = 30f;
    
    void Update()
    {
        // 欧拉角旋转
        transform.eulerAngles = new Vector3(0, 90, 0);
        
        // 使用Rotate方法
        transform.Rotate(Vector3.up * rotateSpeed * Time.deltaTime);
        
        // 四元数旋转
        transform.rotation = Quaternion.Euler(0, 90, 0);
        
        // 平滑旋转
        Quaternion targetRotation = Quaternion.Euler(0, 90, 0);
        transform.rotation = Quaternion.Slerp(
            transform.rotation,
            targetRotation,
            Time.deltaTime * rotateSpeed
        );
    }
    
    // 朝向目标
    public void LookAtTarget(Transform target)
    {
        transform.LookAt(target);
        
        // 自定义上方向
        transform.LookAt(target, Vector3.up);
    }
}
```

### 缩放控制
```csharp
public class ScaleControl : MonoBehaviour
{
    public float scaleSpeed = 1f;
    
    void Update()
    {
        // 直接设置缩放
        transform.localScale = new Vector3(2, 2, 2);
        
        // 平滑缩放
        Vector3 targetScale = new Vector3(2, 2, 2);
        transform.localScale = Vector3.Lerp(
            transform.localScale,
            targetScale,
            Time.deltaTime * scaleSpeed
        );
    }
    
    // 缩放动画
    IEnumerator ScaleAnimation()
    {
        Vector3 originalScale = transform.localScale;
        Vector3 targetScale = originalScale * 2;
        float elapsed = 0f;
        float duration = 1f;
        
        while (elapsed < duration)
        {
            elapsed += Time.deltaTime;
            float progress = elapsed / duration;
            transform.localScale = Vector3.Lerp(
                originalScale,
                targetScale,
                progress
            );
            yield return null;
        }
    }
}
```

## 相对运动

### 父子关系
```csharp
public class HierarchyTransform : MonoBehaviour
{
    public void ManageHierarchy()
    {
        // 设置父对象
        transform.SetParent(parentTransform);
        
        // 设置父对象并保持世界位置
        transform.SetParent(parentTransform, true);
        
        // 解除父子关系
        transform.SetParent(null);
        
        // 获取根对象
        Transform root = transform.root;
        
        // 遍历子对象
        foreach (Transform child in transform)
        {
            Debug.Log($"Child: {child.name}");
        }
    }
}
```

### 相对运动计算
```csharp
public class RelativeMotion : MonoBehaviour
{
    public Transform referenceObject;
    
    void CalculateRelativeMotion()
    {
        // 计算相对位置
        Vector3 relativePosition = 
            referenceObject.InverseTransformPoint(transform.position);
            
        // 计算相对旋转
        Quaternion relativeRotation = 
            Quaternion.Inverse(referenceObject.rotation) * transform.rotation;
            
        // 计算相对速度
        Vector3 relativeVelocity = 
            referenceObject.InverseTransformDirection(GetComponent<Rigidbody>().velocity);
    }
    
    // 跟随目标
    void FollowTarget()
    {
        // 位置跟随
        Vector3 offset = transform.position - referenceObject.position;
        transform.position = referenceObject.position + offset;
        
        // 旋转跟随
        transform.rotation = referenceObject.rotation;
    }
}
```

## 实用工具

### Transform扩展方法
```csharp
public static class TransformExtensions
{
    // 重置Transform
    public static void Reset(this Transform transform)
    {
        transform.localPosition = Vector3.zero;
        transform.localRotation = Quaternion.identity;
        transform.localScale = Vector3.one;
    }
    
    // 复制Transform
    public static void CopyFrom(this Transform transform, Transform source)
    {
        transform.position = source.position;
        transform.rotation = source.rotation;
        transform.localScale = source.localScale;
    }
    
    // 平滑跟随
    public static IEnumerator SmoothFollow(
        this Transform transform,
        Transform target,
        float speed)
    {
        while (true)
        {
            transform.position = Vector3.Lerp(
                transform.position,
                target.position,
                Time.deltaTime * speed
            );
            transform.rotation = Quaternion.Slerp(
                transform.rotation,
                target.rotation,
                Time.deltaTime * speed
            );
            yield return null;
        }
    }
}

## 相关笔记
<!-- AUTO-RELATED-START -->
- [Unity游戏开发基础](04_Unity基础.md)
- [C# 编程语言学习笔记](<../C#.md>)
- [游戏对象和组件](02_游戏对象和组件.md)
- [预制体系统](04_预制体系统.md)
<!-- AUTO-RELATED-END -->
