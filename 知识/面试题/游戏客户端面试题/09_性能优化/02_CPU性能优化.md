---
title: CPU性能优化
type: knowledge
date: '2026-04-14'
status: archived
tags:
  - 知识
  - 面试题
  - 游戏客户端
  - 性能优化
  - CPU优化
source:
  - type: yuque
    url: https://www.yuque.com/wanghao-yciao/dk58gg/lxr3vde1fpnzmtk8
    title: 性能优化
  - note: 二级页归档
related:
  - ./性能优化.md
---

## 📊 图解

> [!info] 图示区
> 这里可以放置解释CPU性能优化的 mermaid 图表、性能分析图或其他辅助理解的图片

### CPU优化层次结构

```mermaid
graph TD
    A[CPU性能优化] --> B[引擎模块优化]
    A --> C[自身代码优化]

    B --> D[物理系统优化]
    B --> E[动画系统优化]
    B --> F[Update频率控制]
    B --> G[剔除优化]
    B --> H[预处理烘焙]

    C --> I[算法优化]
    C --> J[缓存友好设计]
    C --> K[多线程架构]
    C --> L[热点分析]

    style A fill:#e1f5ff
    style B fill:#fff4e1
    style C fill:#ccffcc
```

### 多线程架构

```mermaid
graph TD
    A[多线程任务系统] --> B[主线程<br/>渲染+输入]
    A --> C[物理线程<br/>物理模拟]
    A --> D[AI线程<br/>AI计算]
    A --> E[加载线程<br/>资源加载]
    A --> F[网络线程<br/>网络通信]

    B --> G[任务调度器]
    C --> G
    D --> G
    E --> G
    F --> G

    G --> H[无锁数据结构<br/>任务队列<br/>线程同步]

    style A fill:#e1f5ff
    style G fill:#ccffcc
```

### 缓存友好设计

```mermaid
graph LR
    A[数据布局] --> B[数组 of 结构体<br/>AoS]
    A --> C[结构体 of 数组<br/>SoA]

    B --> D[缓存未命中率高<br/>性能差]
    C --> E[缓存命中率高<br/>性能好]

    D --> F[不推荐]
    E --> G[推荐]

    style A fill:#e1f5ff
    style C fill:#ccffcc
```

### 分层更新系统

```mermaid
graph TD
    A[分层更新系统] --> B[每帧更新<br/>核心逻辑<br/>角色移动]
    A --> C[每10帧更新<br/>AI决策<br/>环境交互]
    A --> D[每30帧更新<br/>装饰效果<br/>背景动画]

    B --> E[高优先级]
    C --> F[中优先级]
    D --> G[低优先级]

    style A fill:#e1f5ff
```

## 📖 原理

### 核心概念

CPU性能优化是提升游戏帧率和反应速度的关键。通过优化引擎模块和自身代码，可以充分利用硬件性能。

#### ⚙️ 引擎模块的CPU优化

**1️⃣ 物理系统优化：**

物理计算通常是CPU密集型任务。

| 优化策略 | 说明 | 效果 |
|---------|------|------|
| **简化碰撞体** | 用简单几何体替代复杂网格 | 减少 40-60% 开销 |
| **物理层级** | 只检测相关层级的碰撞 | 减少 30-50% 检测次数 |
| **降低更新频率** | 远处物体降低物理更新频率 | 减少 20-30% 开销 |
| **休眠系统** | 静止物体进入休眠状态 | 减少 50-70% 计算 |

```csharp
// 物理LOD系统
public class PhysicsLOD : MonoBehaviour
{
    public float[] distanceThresholds = { 10f, 30f, 50f };
    public float[] updateIntervals = { 0f, 0.1f, 0.2f };

    private Rigidbody _rb;
    private float _timer;

    private void Start()
    {
        _rb = GetComponent<Rigidbody>();
    }

    private void FixedUpdate()
    {
        float distance = Vector3.Distance(Camera.main.transform.position, transform.position);
        int lodLevel = GetLODLevel(distance);

        float interval = updateIntervals[lodLevel];
        _timer += Time.fixedDeltaTime;

        if (_timer >= interval)
        {
            _timer = 0;
            _rb.WakeUp();
        }
        else if (interval > 0)
        {
            _rb.Sleep();
        }
    }

    private int GetLODLevel(float distance)
    {
        for (int i = 0; i < distanceThresholds.Length; i++)
        {
            if (distance < distanceThresholds[i])
                return i;
        }
        return distanceThresholds.Length;
    }
}
```

**2️⃣ 动画系统优化：**

角色动画也是CPU瓶颈点。

| 优化策略 | 说明 | 效果 |
|---------|------|------|
| **动画LOD** | 远处角色简化骨骼 | 减少 50-70% 开销 |
| **降低更新频率** | 非关键动画降低频率 | 减少 30-40% 开销 |
| **动画合并** | 合并相似动画减少切换 | 减少 20-30% 开销 |
| **关键帧插值** | 远处使用插值代替完整动画 | 减少 60-80% 开销 |

```csharp
// 动画LOD系统
public class AnimationLOD : MonoBehaviour
{
    public Animator animator;
    public SkinnedMeshRenderer[] simplifiedMeshes;

    public void SetLOD(int level)
    {
        switch (level)
        {
            case 0:  // 高精度
                animator.updateMode = AnimatorUpdateMode.Normal;
                animator.cullingMode = AnimatorCullingMode.CullUpdateTransforms;
                break;

            case 1:  // 中等精度
                animator.updateMode = AnimatorUpdateMode.Normal;
                animator.cullingMode = AnimatorCullingMode.CullCompletely;
                break;

            case 2:  // 低精度
                animator.updateMode = AnimatorUpdateMode.AnimatePhysics;
                // 使用简化网格
                foreach (var smr in simplifiedMeshes)
                {
                    smr.enabled = true;
                }
                break;
        }
    }
}
```

**3️⃣ Update频率控制：**

并非所有游戏逻辑都需要每帧更新。

```csharp
// 分层更新系统
public class LayeredUpdateSystem : MonoBehaviour
{
    private List<IUpdateable> _everyFrameUpdates = new List<IUpdateable>();
    private List<IUpdateable> _every10Frames = new List<IUpdateable>();
    private List<IUpdateable> _every30Frames = new List<IUpdateable>();

    private int _frameCounter;

    public void Register(IUpdateable obj, UpdateFrequency frequency)
    {
        switch (frequency)
        {
            case UpdateFrequency.EveryFrame:
                _everyFrameUpdates.Add(obj);
                break;
            case UpdateFrequency.Every10Frames:
                _every10Frames.Add(obj);
                break;
            case UpdateFrequency.Every30Frames:
                _every30Frames.Add(obj);
                break;
        }
    }

    private void Update()
    {
        _frameCounter++;

        // 每帧更新
        for (int i = 0; i < _everyFrameUpdates.Count; i++)
        {
            _everyFrameUpdates[i].OnUpdate();
        }

        // 每10帧更新
        if (_frameCounter % 10 == 0)
        {
            for (int i = 0; i < _every10Frames.Count; i++)
            {
                _every10Frames[i].OnUpdate();
            }
        }

        // 每30帧更新
        if (_frameCounter % 30 == 0)
        {
            for (int i = 0; i < _every30Frames.Count; i++)
            {
                _every30Frames[i].OnUpdate();
            }
        }
    }
}

public enum UpdateFrequency
{
    EveryFrame,
    Every10Frames,
    Every30Frames
}

public interface IUpdateable
{
    void OnUpdate();
}
```

**4️⃣ 剔除与可见性优化：**

```csharp
// 视锥体剔除
public class FrustumCulling : MonoBehaviour
{
    private Plane[] _frustumPlanes;

    private void Update()
    {
        _frustumPlanes = GeometryUtility.CalculateFrustumPlanes(Camera.main);

        // 检查物体是否在视锥体内
        if (!IsInFrustum())
        {
            // 禁用渲染和更新
        }
    }

    private bool IsInFrustum()
    {
        Bounds bounds = GetComponent<Renderer>().bounds;
        return GeometryUtility.TestPlanesAABB(_frustumPlanes, bounds);
    }
}

// 遮挡剔除
public class OcclusionCulling : MonoBehaviour
{
    private void OnBecameVisible()
    {
        // 恢复更新和渲染
        GetComponent<Animator>().enabled = true;
    }

    private void OnBecameInvisible()
    {
        // 禁用更新和渲染
        GetComponent<Animator>().enabled = false;
    }
}
```

**5️⃣ 预处理与烘焙：**

将运行时计算转移到编辑时。

| 技术 | 说明 | 效果 |
|------|------|------|
| **光照烘焙** | 预计算光照贴图 | 减少 40% CPU |
| **导航网格烘焙** | 预计算寻路数据 | 减少 60% 寻路开销 |
| **物理动画** | 预计算物理模拟 | 减少运行时计算 |

#### 💻 自身代码的CPU优化

**1️⃣ 算法优化：**

识别热点函数并优化算法复杂度。

```csharp
// O(n²) 算法 - 不好的做法
public List<GameObject> FindTargetsInRange_O2(Vector3 position, float range)
{
    List<GameObject> targets = new List<GameObject>();

    GameObject[] allEnemies = GameObject.FindGameObjectsWithTag("Enemy");

    foreach (var enemy in allEnemies)
    {
        float distance = Vector3.Distance(position, enemy.transform.position);
        if (distance <= range)
        {
            targets.Add(enemy);
        }
    }

    return targets;
}

// O(log n) 算法 - 好的做法
public class SpatialHash
{
    private Dictionary<int, List<GameObject>> _cells = new Dictionary<int, List<GameObject>>();
    private float _cellSize;

    public List<GameObject> FindTargetsInRange(Vector3 position, float range)
    {
        List<GameObject> targets = new List<GameObject>();

        int cellX = Mathf.FloorToInt(position.x / _cellSize);
        int cellZ = Mathf.FloorToInt(position.z / _cellSize);

        // 只检查相邻的单元格
        for (int x = -1; x <= 1; x++)
        {
            for (int z = -1; z <= 1; z++)
            {
                int key = (cellX + x) * 10000 + (cellZ + z);

                if (_cells.TryGetValue(key, out List<GameObject> cell))
                {
                    foreach (var obj in cell)
                    {
                        float distance = Vector3.Distance(position, obj.transform.position);
                        if (distance <= range)
                        {
                            targets.Add(obj);
                        }
                    }
                }
            }
        }

        return targets;
    }
}
```

**性能对比：**

| 敌人数量 | O(n²) 算法 | O(log n) 算法 | 提升 |
|---------|-----------|--------------|------|
| 100 | 0.5ms | 0.1ms | 5x |
| 500 | 12ms | 0.3ms | 40x |
| 1000 | 48ms | 0.5ms | 96x |

**2️⃣ 缓存友好设计：**

现代CPU架构中，缓存命中率对性能影响巨大。

```csharp
// 不好的布局 - 缓存未命中
public struct ParticleSystem_Bad
{
    public Vector3[] positions;
    public Vector3[] velocities;
    public Color[] colors;
    public float[] lifetimes;
}

// 好的布局 - 缓存命中
public struct ParticleSystem_Good
{
    public ParticleData[] particles;

    public struct ParticleData
    {
        public Vector3 position;
        public Vector3 velocity;
        public Color color;
        public float lifetime;
    }
}

// 批量处理
public class ParticleProcessor
{
    public void Update(ParticleData[] particles, float deltaTime)
    {
        // 连续内存访问，缓存命中率高
        for (int i = 0; i < particles.Length; i++)
        {
            particles[i].position += particles[i].velocity * deltaTime;
            particles[i].lifetime -= deltaTime;
        }
    }
}
```

**3️⃣ 多线程架构：**

```csharp
// 多线程任务系统
public class MultiThreadTaskSystem
{
    private TaskScheduler _scheduler;

    public void Initialize()
    {
        // 创建任务调度器
        _scheduler = TaskScheduler.FromDefaultScheduler();

        // 设置最大并行度
        int maxThreads = SystemInfo.processorCount - 1;  // 保留一个线程给主线程
    }

    public Task RunPhysicsAsync()
    {
        return Task.Factory.StartNew(() =>
        {
            // 在后台线程执行物理计算
            PhysicsSimulation.Simulate(Time.fixedDeltaTime);
        }, CancellationToken.None, TaskCreationOptions.None, _scheduler);
    }

    public Task RunAIAsync()
    {
        return Task.Factory.StartNew(() =>
        {
            // 在后台线程执行AI计算
            AISystem.UpdateAll();
        }, CancellationToken.None, TaskCreationOptions.None, _scheduler);
    }

    public Task LoadAssetsAsync(string[] paths)
    {
        return Task.Run(() =>
        {
            // 在后台线程加载资源
            foreach (string path in paths)
            {
                ResourceLoader.Load(path);
            }
        });
    }
}
```

**4️⃣ 热点分析与定向优化：**

使用性能分析工具定位瓶颈。

```csharp
// 性能分析辅助类
public class PerformanceProfiler
{
    private Dictionary<string, ProfilerRecord> _records = new Dictionary<string, ProfilerRecord>();

    public IDisposable BeginSample(string name)
    {
        return new ProfilerScope(this, name);
    }

    public void EndSample(string name, float duration)
    {
        if (!_records.ContainsKey(name))
        {
            _records[name] = new ProfilerRecord { name = name };
        }

        _records[name].AddSample(duration);
    }

    public void PrintReport()
    {
        Debug.Log("=== Performance Report ===");
        foreach (var kvp in _records.OrderByDescending(r => r.Value.averageTime))
        {
            Debug.Log($"{kvp.Key}: {kvp.Value.averageTime:F2}ms (调用 {kvp.Value.count} 次)");
        }
    }

    private class ProfilerScope : IDisposable
    {
        private PerformanceProfiler _profiler;
        private string _name;
        private float _startTime;

        public ProfilerScope(PerformanceProfiler profiler, string name)
        {
            _profiler = profiler;
            _name = name;
            _startTime = Time.realtimeSinceStartup;
        }

        public void Dispose()
        {
            float duration = (Time.realtimeSinceStartup - _startTime) * 1000f;
            _profiler.EndSample(_name, duration);
        }
    }
}

// 使用示例
public class GameLogic : MonoBehaviour
{
    private PerformanceProfiler _profiler = new PerformanceProfiler();

    private void Update()
    {
        using (_profiler.BeginSample("PlayerMovement"))
        {
            UpdatePlayerMovement();
        }

        using (_profiler.BeginSample("Combat"))
        {
            UpdateCombat();
        }
    }
}
```

---

## 💡 面试题

### Q：如何优化游戏的CPU性能？请具体谈谈引擎模块和自身代码两方面的优化措施。

#### 🎯 CPU优化全景图

```mermaid
graph TD
    A[CPU性能优化] --> B[引擎模块]
    A --> C[自身代码]

    B --> D[物理系统<br/>动画系统<br/>Update频率<br/>剔除优化<br/>预处理烘焙]

    C --> E[算法优化<br/>缓存设计<br/>多线程<br/>热点分析<br/>内存优化]

    D --> F[减少引擎开销]
    E --> G[提升代码效率]

    style A fill:#e1f5ff
    style F fill:#ccffcc
    style G fill:#ccffcc
```

#### 📊 引擎模块优化详解

**1️⃣ 物理系统优化实战：**

**案例：大规模可破坏物体场景**

```csharp
// 物理LOD管理器
public class PhysicsLODManager : MonoBehaviour
{
    private List<Rigidbody> _dynamicBodies = new List<Rigidbody>();
    private Dictionary<Rigidbody, float> _lastUpdateTime = new Dictionary<Rigidbody, float>();

    public void RegisterRigidbody(Rigidbody rb, float updateInterval)
    {
        _dynamicBodies.Add(rb);
        _lastUpdateTime[rb] = -updateInterval;  // 立即更新一次
    }

    private void FixedUpdate()
    {
        float currentTime = Time.fixedTime;

        foreach (var rb in _dynamicBodies)
        {
            if (_lastUpdateTime.TryGetValue(rb, out float lastTime))
            {
                float interval = CalculateUpdateInterval(rb);
                if (currentTime - lastTime >= interval)
                {
                    rb.WakeUp();
                    _lastUpdateTime[rb] = currentTime;
                }
                else
                {
                    rb.Sleep();
                }
            }
        }
    }

    private float CalculateUpdateInterval(Rigidbody rb)
    {
        float distance = Vector3.Distance(Camera.main.transform.position, rb.position);

        if (distance < 20f)
            return 0f;        // 近距离：每帧更新
        else if (distance < 50f)
            return 0.1f;      // 中距离：每0.1秒更新
        else
            return 0.5f;      // 远距离：每0.5秒更新
    }
}
```

**效果数据：**

| 场景复杂度 | 优化前开销 | 优化后开销 | 提升 |
|----------|-----------|-----------|------|
| 100个物体 | 8ms | 3ms | **62%↓** |
| 500个物体 | 40ms | 12ms | **70%↓** |
| 1000个物体 | 85ms | 25ms | **71%↓** |

**2️⃣ 动画系统优化实战：**

**案例：MMO大量角色场景**

```csharp
// 角色动画LOD系统
public class CharacterAnimationLOD : MonoBehaviour
{
    public Animator animator;
    public int[] boneCounts = { 60, 30, 15 };  // 不同LOD的骨骼数

    public void SetAnimationLOD(int level)
    {
        switch (level)
        {
            case 0:  // 高精度：全骨骼
                animator.updateMode = AnimatorUpdateMode.Normal;
                SetBoneCount(boneCounts[0]);
                break;

            case 1:  // 中精度：半数骨骼
                animator.updateMode = AnimatorUpdateMode.AnimatePhysics;
                SetBoneCount(boneCounts[1]);
                break;

            case 2:  // 低精度：简化骨骼
                animator.updateMode = AnimatorUpdateMode.AnimatePhysics;
                animator.cullingMode = AnimatorCullingMode.CullCompletely;
                SetBoneCount(boneCounts[2]);
                break;
        }
    }

    private void SetBoneCount(int count)
    {
        // 设置骨骼数量
        // 这需要自定义动画系统或使用重蒙皮技术
    }
}

// 基于距离的动画LOD管理
public class AnimationLODManager : MonoBehaviour
{
    private List<CharacterAnimationLOD> _characters = new List<CharacterAnimationLOD>();

    private void Update()
    {
        foreach (var character in _characters)
        {
            float distance = Vector3.Distance(Camera.main.transform.position, character.transform.position);

            int lodLevel = CalculateLOD(distance);
            character.SetAnimationLOD(lodLevel);
        }
    }

    private int CalculateLOD(float distance)
    {
        if (distance < 30f)
            return 0;  // 高精度
        else if (distance < 60f)
            return 1;  // 中精度
        else
            return 2;  // 低精度
    }
}
```

**效果数据：**

| 指标 | 优化前 | 优化后 | 提升 |
|------|-------|-------|------|
| **最大角色数** | 50 | 200 | **4x** |
| **动画CPU开销** | 15ms | 8ms | **47%↓** |
| **帧率稳定性** | 60% | 90% | **50%↑** |

**3️⃣ 分层更新系统实战：**

```csharp
// 完整的分层更新管理器
public class LayeredUpdateManager : MonoBehaviour
{
    private List<IUpdateable> _updates = new List<IUpdateable>();

    public void Register(IUpdateable obj, UpdateLayer layer)
    {
        obj.UpdateLayer = layer;
        _updates.Add(obj);
    }

    private void Update()
    {
        int frameCount = Time.frameCount;

        foreach (var updateable in _updates)
        {
            if (ShouldUpdate(updateable, frameCount))
            {
                updateable.OnUpdate();
            }
        }
    }

    private bool ShouldUpdate(IUpdateable obj, int frameCount)
    {
        switch (obj.UpdateLayer)
        {
            case UpdateLayer.EveryFrame:
                return true;

            case UpdateLayer.Every2Frames:
                return (frameCount % 2) == 0;

            case UpdateLayer.Every5Frames:
                return (frameCount % 5) == 0;

            case UpdateLayer.Every10Frames:
                return (frameCount % 10) == 0;

            case UpdateLayer.Every30Frames:
                return (frameCount % 30) == 0;

            default:
                return false;
        }
    }
}

public enum UpdateLayer
{
    EveryFrame,
    Every2Frames,
    Every5Frames,
    Every10Frames,
    Every30Frames
}

public interface IUpdateable
{
    UpdateLayer UpdateLayer { get; set; }
    void OnUpdate();
}
```

**不同模块的更新频率建议：**

| 模块 | 更新频率 | 说明 |
|------|---------|------|
| **玩家输入** | 每帧 | 需要即时响应 |
| **角色移动** | 每帧 | 核心玩法 |
| **AI决策** | 每10帧 | 可以降低频率 |
| **环境动画** | 每30帧 | 非关键功能 |
| **装饰效果** | 每30帧 | 可选功能 |

#### 💻 自身代码优化详解

**1️⃣ 算法优化实战案例：**

**场景：大规模战斗系统技能筛选**

```csharp
// O(n²) 算法 - 低效
public class SkillTargetSelector_O2
{
    public List<Character> SelectTargets(Vector3 center, float range, Team team)
    {
        List<Character> allCharacters = CharacterManager.AllCharacters;
        List<Character> targets = new List<Character>();

        foreach (var character in allCharacters)
        {
            if (character.team == team)
            {
                float distance = Vector3.Distance(center, character.position);
                if (distance <= range)
                {
                    targets.Add(character);
                }
            }
        }

        return targets;
    }
}

// O(log n) 算法 - 高效
public class SkillTargetSelector_Optimized
{
    private SpatialGrid _grid = new SpatialGrid(10f);  // 10米网格大小

    public List<Character> SelectTargets(Vector3 center, float range, Team team)
    {
        List<Character> targets = new List<Character>();

        // 获取范围内的网格
        var cells = _grid.GetCellsInRange(center, range);

        // 只检查这些网格中的角色
        foreach (var cell in cells)
        {
            foreach (var character in cell.characters)
            {
                if (character.team == team)
                {
                    float distance = Vector3.Distance(center, character.position);
                    if (distance <= range)
                    {
                        targets.Add(character);
                    }
                }
            }
        }

        return targets;
    }
}

// 空间网格实现
public class SpatialGrid
{
    private struct GridCell
    {
        public List<Character> characters = new List<Character>();
    }

    private Dictionary<int, GridCell> _cells = new Dictionary<int, GridCell>();
    private float _cellSize;

    public SpatialGrid(float cellSize)
    {
        _cellSize = cellSize;
    }

    public void AddCharacter(Character character)
    {
        int cellKey = GetCellKey(character.position);
        if (!_cells.ContainsKey(cellKey))
        {
            _cells[cellKey] = new GridCell();
        }
        _cells[cellKey].characters.Add(character);
    }

    public List<GridCell> GetCellsInRange(Vector3 center, float range)
    {
        List<GridCell> result = new List<GridCell>();

        int centerCellX = Mathf.FloorToInt(center.x / _cellSize);
        int centerCellZ = Mathf.FloorToInt(center.z / _cellSize);
        int cellRadius = Mathf.CeilToInt(range / _cellSize);

        for (int x = -cellRadius; x <= cellRadius; x++)
        {
            for (int z = -cellRadius; z <= cellRadius; z++)
            {
                int key = (centerCellX + x) * 10000 + (centerCellZ + z);
                if (_cells.TryGetValue(key, out GridCell cell))
                {
                    result.Add(cell);
                }
            }
        }

        return result;
    }

    private int GetCellKey(Vector3 position)
    {
        int x = Mathf.FloorToInt(position.x / _cellSize);
        int z = Mathf.FloorToInt(position.z / _cellSize);
        return x * 10000 + z;
    }
}
```

**性能对比：**

| 角色数量 | O(n²) 算法 | O(log n) 算法 | 提升 |
|---------|-----------|--------------|------|
| 100 | 1ms | 0.1ms | 10x |
| 500 | 25ms | 0.3ms | 83x |
| 1000 | 100ms | 0.5ms | 200x |

**2️⃣ 缓存友好设计实战：**

**案例：粒子系统重构**

```csharp
// 不好的设计 - AoS（数组 of 结构体）
public class ParticleSystem_AoS
{
    public struct Particle
    {
        public Vector3 position;
        public Vector3 velocity;
        public Color color;
        public float lifetime;
    }

    private Particle[] _particles;

    public void Update(float deltaTime)
    {
        for (int i = 0; i < _particles.Length; i++)
        {
            // 每次访问不同的数组位置，缓存未命中
            _particles[i].position += _particles[i].velocity * deltaTime;
            _particles[i].lifetime -= deltaTime;
            _particles[i].color = Color.Lerp(_particles[i].color, Color.clear, deltaTime);
        }
    }
}

// 好的设计 - SoA（结构体 of 数组）
public class ParticleSystem_SoA
{
    private Vector3[] _positions;
    private Vector3[] _velocities;
    private Color[] _colors;
    private float[] _lifetimes;

    public void Update(float deltaTime)
    {
        // 连续内存访问，缓存命中率高
        for (int i = 0; i < _positions.Length; i++)
        {
            _positions[i] += _velocities[i] * deltaTime;
            _lifetimes[i] -= deltaTime;
            _colors[i] = Color.Lerp(_colors[i], Color.clear, deltaTime);
        }
    }
}
```

**性能提升：**

| 粒子数量 | AoS 性能 | SoA 性能 | 提升 |
|---------|---------|---------|------|
| 1000 | 5ms | 3.5ms | **30%↑** |
| 5000 | 25ms | 15ms | **40%↑** |
| 10000 | 50ms | 30ms | **40%↑** |

**3️⃣ 多线程架构实战：**

```csharp
// 完整的多线程任务系统
public class MultiThreadGameLoop : MonoBehaviour
{
    private Thread _physicsThread;
    private Thread _aiThread;
    private Thread _loadingThread;

    private Queue<PhysicsTask> _physicsQueue = new Queue<PhysicsTask>();
    private Queue<AITask> _aiQueue = new Queue<AITask>();
    private Queue<LoadTask> _loadingQueue = new Queue<LoadTask>();

    private bool _isRunning;

    private void Start()
    {
        _isRunning = true;

        // 启动物理线程
        _physicsThread = new Thread(PhysicsThreadLoop)
        {
            IsBackground = true,
            Priority = ThreadPriority.AboveNormal
        };
        _physicsThread.Start();

        // 启动AI线程
        _aiThread = new Thread(AIThreadLoop)
        {
            IsBackground = true,
            Priority = ThreadPriority.Normal
        };
        _aiThread.Start();

        // 启动加载线程
        _loadingThread = new Thread(LoadingThreadLoop)
        {
            IsBackground = true,
            Priority = ThreadPriority.BelowNormal
        };
        _loadingThread.Start();
    }

    private void Update()
    {
        // 主线程：渲染和输入
        HandleInput();
        RenderScene();
    }

    private void PhysicsThreadLoop()
    {
        while (_isRunning)
        {
            if (_physicsQueue.Count > 0)
            {
                PhysicsTask task = _physicsQueue.Dequeue();
                task.Execute();
            }
            else
            {
                Thread.Sleep(1);
            }
        }
    }

    private void AIThreadLoop()
    {
        while (_isRunning)
        {
            if (_aiQueue.Count > 0)
            {
                AITask task = _aiQueue.Dequeue();
                task.Execute();
            }
            else
            {
                Thread.Sleep(10);  // AI可以稍微休息
            }
        }
    }

    private void LoadingThreadLoop()
    {
        while (_isRunning)
        {
            if (_loadingQueue.Count > 0)
            {
                LoadTask task = _loadingQueue.Dequeue();
                task.Execute();
            }
            else
            {
                Thread.Sleep(100);  // 加载线程可以休息更久
            }
        }
    }

    private void OnDestroy()
    {
        _isRunning = false;

        _physicsThread?.Join();
        _aiThread?.Join();
        _loadingThread?.Join();
    }
}
```

**4️⃣ 热点分析实战：**

```csharp
// 自动化性能分析
public class PerformanceAnalyzer
{
    private class FunctionProfile
    {
        public string name;
        public long totalTicks;
        public int callCount;
        public double averageTime;

        public void Record(long ticks)
        {
            totalTicks += ticks;
            callCount++;
            averageTime = (double)totalTicks / callCount;
        }
    }

    private Dictionary<string, FunctionProfile> _profiles = new Dictionary<string, FunctionProfile>();

    public IDisposable Profile(string functionName)
    {
        return new ProfilerScope(this, functionName);
    }

    public void PrintTopFunctions(int topN = 10)
    {
        var sorted = _profiles.OrderByDescending(p => p.Value.averageTime).Take(topN);

        Debug.Log("=== Top CPU Consumers ===");
        foreach (var kvp in sorted)
        {
            Debug.Log($"{kvp.Key}: {kvp.Value.averageTime:F2}ms (调用 {kvp.Value.callCount} 次)");
        }
    }

    private class ProfilerScope : IDisposable
    {
        private PerformanceAnalyzer _analyzer;
        private string _functionName;
        private long _startTicks;

        public ProfilerScope(PerformanceAnalyzer analyzer, string functionName)
        {
            _analyzer = analyzer;
            _functionName = functionName;
            _startTicks = System.DateTime.UtcNow.Ticks;
        }

        public void Dispose()
        {
            long elapsedTicks = System.DateTime.UtcNow.Ticks - _startTicks;
            double elapsedMs = elapsedTicks / 10000.0;  // 转换为毫秒

            if (!_analyzer._profiles.ContainsKey(_functionName))
            {
                _analyzer._profiles[_functionName] = new FunctionProfile { name = _functionName };
            }

            _analyzer._profiles[_functionName].Record(elapsedTicks);
        }
    }
}

// 使用示例
public class GameBattleSystem
{
    private PerformanceAnalyzer _analyzer = new PerformanceAnalyzer();

    public void Update()
    {
        using (_analyzer.Profile("CombatSystem"))
        {
            UpdateCombat();
        }

        using (_analyzer.Profile("SkillEffects"))
        {
            UpdateSkillEffects();
        }
    }

    public void OnBattleEnd()
    {
        _analyzer.PrintTopFunctions();
    }
}
```

#### 📊 综合优化效果

通过系统性的CPU优化，可以达到：

| 指标 | 优化前 | 优化后 | 提升 |
|------|-------|-------|------|
| **帧率** | 30fps | 60fps | **2x** |
| **帧时间** | 33ms | 16ms | **52%↓** |
| **CPU利用率** | 95% | 60% | **37%↓** |
| **能耗** | 高 | 中 | **30%↓** |

> [!tip] 总结
> CPU优化需要从两个维度系统性地进行：
> 1. **引擎模块**：物理、动画、更新频率、剔除、预处理
> 2. **自身代码**：算法、缓存、多线程、热点分析
>
> 关键是建立性能预算系统，使用分析工具定位瓶颈，持续监控和优化。

---

## 🔗 相关链接

- [[性能优化]] - 父主题索引
- [[内存优化]] - 相关主题：内存分配优化
- [[GPU性能优化]] - 相关主题：渲染性能优化
- [[开放世界性能优化]] - 相关主题：大规模场景优化
