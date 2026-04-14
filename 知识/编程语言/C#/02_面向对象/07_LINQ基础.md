---
title: LINQ基础
type: knowledge
date: '2026-04-12'
status: distilled
tags:
- 知识
- 编程语言
- C#
- 面向对象
source: []
related:
- C# 编程语言学习笔记
- 泛型
---
# LINQ基础

## LINQ查询语法

### 基本查询
```csharp
public class Item
{
    public string Name { get; set; }
    public int Level { get; set; }
    public float Price { get; set; }
}

// 查询语法示例
public class InventorySystem
{
    private List<Item> items = new List<Item>();
    
    public IEnumerable<Item> GetHighLevelItems()
    {
        var query = from item in items
                   where item.Level >= 10
                   orderby item.Level descending
                   select item;
                   
        return query;
    }
    
    public IEnumerable<string> GetItemNames()
    {
        var query = from item in items
                   select item.Name;
                   
        return query;
    }
}
```

### 复杂查询
```csharp
public class GameManager
{
    private List<Player> players;
    private List<Achievement> achievements;
    
    public IEnumerable<PlayerStats> GetPlayerStats()
    {
        var query = from player in players
                   join achievement in achievements
                   on player.Id equals achievement.PlayerId
                   group achievement by player into playerAchievements
                   select new PlayerStats
                   {
                       PlayerName = playerAchievements.Key.Name,
                       AchievementCount = playerAchievements.Count(),
                       TotalScore = playerAchievements.Sum(a => a.Score)
                   };
                   
        return query;
    }
}
```

## LINQ方法语法

### 基本方法
```csharp
public class EntityManager
{
    private List<GameObject> gameObjects;
    
    // 筛选
    public IEnumerable<GameObject> GetActiveObjects()
    {
        return gameObjects.Where(obj => obj.activeInHierarchy);
    }
    
    // 排序
    public IEnumerable<GameObject> GetSortedByDistance(Vector3 position)
    {
        return gameObjects
            .OrderBy(obj => Vector3.Distance(obj.transform.position, position));
    }
    
    // 转换
    public IEnumerable<string> GetObjectNames()
    {
        return gameObjects.Select(obj => obj.name);
    }
}
```

### 聚合方法
```csharp
public class ScoreManager
{
    private List<PlayerScore> scores;
    
    public float GetAverageScore()
    {
        return scores.Average(s => s.Value);
    }
    
    public int GetTotalScore()
    {
        return scores.Sum(s => s.Value);
    }
    
    public PlayerScore GetHighestScore()
    {
        return scores.MaxBy(s => s.Value);
    }
}
```

## LINQ在Unity中的应用

### 游戏对象查询
```csharp
public class ObjectFinder : MonoBehaviour
{
    // 查找特定标签的对象
    public IEnumerable<GameObject> FindObjectsWithTag(string tag)
    {
        return GameObject.FindObjectsOfType<GameObject>()
            .Where(obj => obj.CompareTag(tag));
    }
    
    // 查找特定组件
    public IEnumerable<T> FindComponentsInRange<T>(Vector3 position, float range)
        where T : Component
    {
        return GameObject.FindObjectsOfType<T>()
            .Where(comp => Vector3.Distance(comp.transform.position, position) <= range);
    }
}
```

### 组件管理
```csharp
public class ComponentManager : MonoBehaviour
{
    private List<MonoBehaviour> components;
    
    // 查找并禁用特定类型的组件
    public void DisableComponents<T>() where T : MonoBehaviour
    {
        components
            .OfType<T>()
            .ToList()
            .ForEach(comp => comp.enabled = false);
    }
    
    // 获取所有启用的组件
    public IEnumerable<MonoBehaviour> GetEnabledComponents()
    {
        return components.Where(comp => comp.enabled);
    }
}
```

## LINQ优化

### 延迟执行
```csharp
public class PerformanceExample
{
    private IEnumerable<GameObject> objects;
    
    // 避免多次枚举
    public void ProcessObjects()
    {
        // 不好的做法
        var count = objects.Count();
        var first = objects.FirstOrDefault();
        // 每次调用都会重新枚举
        
        // 好的做法
        var objectsList = objects.ToList();
        count = objectsList.Count;
        first = objectsList.FirstOrDefault();
    }
    
    // 使用延迟执行优化性能
    public IEnumerable<GameObject> GetFilteredObjects()
    {
        return objects
            .Where(obj => obj != null)  // 延迟执行
            .Select(obj => obj);        // 延迟执行
    }
}
```

### 性能优化技巧
```csharp
public class OptimizedQueries
{
    // 使用适当的LINQ方法
    public bool HasAnyActiveObjects(IEnumerable<GameObject> objects)
    {
        return objects.Any(obj => obj.activeInHierarchy);
        // 比 objects.Count(obj => obj.activeInHierarchy) > 0 更高效
    }
    
    // 避免不必要的排序
    public GameObject GetClosestObject(IEnumerable<GameObject> objects, Vector3 position)
    {
        return objects.MinBy(obj => 
            Vector3.Distance(obj.transform.position, position));
        // 比 OrderBy().First() 更高效
    }
}
```

## 自定义LINQ操作符

### 扩展方法
```csharp
public static class LinqExtensions
{
    // 自定义随机选择扩展方法
    public static T RandomElement<T>(this IEnumerable<T> source)
    {
        if (source == null)
            throw new ArgumentNullException(nameof(source));
            
        var list = source as IList<T> ?? source.ToList();
        if (list.Count == 0)
            throw new InvalidOperationException("Sequence contains no elements");
            
        return list[Random.Range(0, list.Count)];
    }
    
    // 自定义洗牌扩展方法
    public static IEnumerable<T> Shuffle<T>(this IEnumerable<T> source)
    {
        var list = source.ToList();
        for (int i = list.Count - 1; i > 0; i--)
        {
            int j = Random.Range(0, i + 1);
            T temp = list[i];
            list[i] = list[j];
            list[j] = temp;
        }
        return list;
    }
}

// 使用示例
public class GameManager
{
    private List<Item> items;
    
    public Item GetRandomItem()
    {
        return items.RandomElement();
    }
    
    public IEnumerable<Item> GetShuffledItems()
    {
        return items.Shuffle();
    }
}

## 相关笔记
<!-- AUTO-RELATED-START -->
- [C# 面向对象编程](<../C#.md>)
- [C# 编程语言学习笔记](<../C#.md>)
- [泛型](06_泛型.md)
<!-- AUTO-RELATED-END -->
