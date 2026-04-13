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
---

# Transform和坐标系统

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
