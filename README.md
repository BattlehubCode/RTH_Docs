# Runtime Transform Handles for Unity

Welcome to the [**Runtime Transform Handels v.4.5.6**](https://assetstore.unity.com/packages/tools/modeling/runtime-transform-handles-65363) documentation. 

[![Asset Store][youtube_icon]](https://assetstore.unity.com/packages/tools/modeling/runtime-transform-handles-65363)

> **Note**  
	**Documentation for the previous versions can be found** [**here**](https://rteditor.battlehub.net/v350/manual/transform-handles.html).

> **Note**
	Runtime Transform Handles are part of a larger asset called [Runtime Editor](https://assetstore.unity.com/packages/tools/modeling/runtime-editor-64806) .
	
# Contents

<!-- TOC start (generated with https://github.com/derlin/bitdowntoc) -->

- [Runtime Transform Handles for Unity](#runtime-transform-handles-for-unity)
- [Contents](#contents)
- [Introduction](#introduction)
- [Getting Started](#getting-started)
- [Example Scenes](#example-scenes)
- [Universal Render Pipeline Support](#universal-render-pipeline-support)
- [HDRP Support](#hdrp-support)
- [Expose To Editor](#expose-to-editor)
- [Create Menu](#create-menu)
- [Position Handle](#position-handle)
- [Rotation, Scale Handle, Rect Tool](#rotation-scale-handle-rect-tool)
- [Scene Grid](#scene-grid)
- [Scene Gizmo](#scene-gizmo)
- [Colors and Sizes](#colors-and-sizes)
- [Scene Component](#scene-component)
- [Select Which Camera the Scene Component Will Control](#select-which-camera-the-scene-component-will-control)
- [Scene Component (No Camera Controls)](#scene-component-no-camera-controls)
- [Select Current Transform Handle](#select-current-transform-handle)
- [Runtime Undo](#runtime-undo)
- [IOC](#ioc)
- [Runtime Selection](#runtime-selection)
      + [Properties](#properties)
      + [Events](#events)
      + [Methods](#methods)
- [Runtime Tools](#runtime-tools)
      + [Example Usage](#example-usage)
- [Runtime Undo](#runtime-undo-1)
      + [Example Usage](#example-usage-1)
      + [Mobile Scene (Touch Support)](#mobile-scene-touch-support)
      + [Demo Editor](#demo-editor)
- [Support](#support)

<!-- TOC end -->

# Introduction
**Runtime Transform Handles** are the runtime 3D controls used to manipulate GameObjects in the scene. 
There are three built-in transform tools for positioning, rotating, and scaling objects via the transform component. 
Supplementary controls, such as the scene gizmo and grid, allow for changing the viewing angle and projection mode, and for showing the grid in the scene. 
Other important components include the selection component, scene component, and handles component, which allow for selecting objects, navigating the scene, and changing the appearance of transform handles.

# Getting Started
1. Create a new scene.
2. Click **Tools -> Runtime Handles -> Create Demo Editor**.
   ![Create RuntimeEditor][getting_started_1]
3. Alternatively, click **Tools -> Runtime Editor -> Create Editor**.
4. Create several Game Objects and add the [Expose To Editor](#expose-to-editor) component.
	![Add Expose To Editor][getting_started_3]
5. Press "Play".
	![Getting Started Result][getting_started_5]

# Example Scenes
There are various example scenes available. To access them: click **Tools -> Runtime Handles -> Show me examples**.
	![Show Me Examples][example_scenes_2]
	

# Universal Render Pipeline Support
1. Unpack "Assets/Battlehub/**UniversalRP Support [RTHandles]**".
   ![Unpack 1 Universal RP][universal_rp_1]
2. Click "Install/Upgrade" dependencies to install the required Universal Render Pipeline package.
3. Click **Tools -> Runtime Handles -> Use URP RenderPipelineAsset**.
4. Click **Tools -> Runtime Handles -> Add URP support**.
   ![Add Required Prefabs][universal_rp_2]
5. You should see the following game object in the scene
   ![Scene Asset][universal_rp_3]
6. And the following render pipeline asset will be used
   ![Render Pipeline Asset][universal_rp_4]

7. If you want to use your own rendering pipeline asset, be sure to add the following Renderer Features:
	* Render Gizmos
	* Render Selection
	
	![Scriptable render features][universal_rp_5]


# HDRP Support
The procedure is the same as for [URP Support](#universal-render-pipeline-support), except you need to unpack Assets/Battlehub/**HDRP Support [RTHandles]** and use the corresponding **HDRP** menu items.

# Expose To Editor
Add the Assets/Battlehub/RTEditor/Runtime/RTCommon/**ExposeToEditor** component to any Game Object you want to make available for selection and editing.

![Expose To Editor][expose_to_editor]

1. **Selection Events**:
   - **Selected (ExposeToEditor)**
   - **Unselected (ExposeToEditor)**
	
2. **Bounds Configuration**:
   - **Bounds Object**: Specifies the GameObject to which the collider will be added (if AddColliders == false)
   - **Bounds Type**: Determines the type of bounds to be used.
   - **Custom Bounds**: If Bounds Type is set to Custom, it allows for specifying custom bounds with center and extents.
   
3. **Capabilities**:
	- **Can Transform**, **Can Inspect**, **Can Duplicate**, **Can Delete**, **Can Rename**, **Can Create Prefab**, **Show Selection Gizmo**: These options are self-explanatory.

4. **Collider Management**:
   - **Add Colliders**: You can unselect the Add Colliders option if you want to specify the collider to be used for selection yourself. If this option is selected, a MeshCollider will be added automatically..
   
# Create Menu
Transform handles and components are organized into prefabs and scripts and can be used together or separately. To create the required transform handle or component, use Tools -> Runtime Handles -> Create.
![Create Menu][create_menu]

# Position Handle
The Position Handle can be used either as a separate component on a GameObject that you wish to move or as a standalone GameObject with the PositionHandle component. 
This component allows you to move one or multiple GameObjects referenced through the `Targets` Property.

1. To create a position handle as a separate component, select a GameObject in the scene and click:
	- **Tools -> Runtime Handles -> Create -> Position Handle (Light).**

2. To create standalone GameObject use:
	- **Tools -> Runtime Handles -> Create -> Position Handle**

3. Click Play and you should see following:
	![Position Handle][position_handle]
	
The difference between `PositionHandle (Light)` and `PositionHandle` is that `PositionHandle` uses a separate GameObject (`PositionHandleModel`) to render itself, 
whereas `PositionHandle (Light)` is a single component that renders itself using commands issued on command buffers. 
The purpose of the separate model used by `PositionHandle` is to render lines with configurable thickness on platforms where geometry shaders are not supported.


Creating a position handle programmatically is as easy as calling `AddComponent` on a GameObject:
```csharp
using Battlehub.RTHandles;
using Unity.VisualScripting;
using UnityEngine;

public class CreatePositionHandleProgrammatically : MonoBehaviour
{   
    void Start()
    {
        var cube = GameObject.CreatePrimitive(PrimitiveType.Cube).transform;
        var capsue = GameObject.CreatePrimitive(PrimitiveType.Capsule).transform;
        var sphere = GameObject.CreatePrimitive(PrimitiveType.Sphere).transform;

        cube.position = new Vector3(2, 0, 0);
        capsue.position = new Vector3(0, 0, 0);
        sphere.position = new Vector3(-2, 0, 0);

        cube.AddComponent<PositionHandle>();
        capsue.AddComponent<PositionHandle>();  
        sphere.AddComponent<PositionHandle>();  
    }
}

```

![Multiple Position Handles][position_handle_2]
	

The main points of interest are the Targets property and the Before Drag, Drag, and Drop events, which can be set using the following script:
```csharp
using Battlehub.RTHandles;
using UnityEngine;

public class PositionHandleUsageExample : MonoBehaviour
{   
    void Start()
    {
        var positionHandle = FindObjectOfType<PositionHandle>();

        var cube = GameObject.CreatePrimitive(PrimitiveType.Cube).transform;
        var capsue = GameObject.CreatePrimitive(PrimitiveType.Capsule).transform;
        var sphere = GameObject.CreatePrimitive(PrimitiveType.Sphere).transform;

        cube.position = new Vector3(2, 0, 0);
        capsue.position = new Vector3(0, 0, 0);
        sphere.position = new Vector3(-2, 0, 0);

        positionHandle.Targets = new[] { cube, capsue, sphere };

        positionHandle.BeforeDrag.AddListener(OnBeforeDrag);
        positionHandle.Drag.AddListener(OnDrag);
        positionHandle.Drop.AddListener(OnDrop);
    }

    private void OnBeforeDrag(BaseHandle handle)
    {
        Debug.Log($"On Before Drag {handle.name}");
    }

    private void OnDrag(BaseHandle handle)
    {
        Debug.Log($"On Drag {handle.name}, {handle.transform.position}");
    }

    private void OnDrop(BaseHandle handle)
    {
        Debug.Log($"On Drop {handle.name}");
    }
}
```

`HighlightOnHover`, `EnableUndo`, and `GridSize` allow you to control specific behaviors of the position handle:

- `HighlightOnHover`: Enables the behavior where the position handle changes its color on pointer hover events.
- `EnableUndo`: Enables the recording of position handle movements in the undo stack.
- `GridSize`: Specifies the step size for dragging the position handle when the Shift key is pressed.

Other properties are required to hold references to several infrastructure components of the Runtime Transform Handles package.

# Rotation, Scale Handle, Rect Tool

To create a `Rotation`, `Scale Handle` or `Rect Tool`, use the corresponding commands from the [Create Menu](#create-menu). 
Everything mentioned regarding the [Position Handle](#position-handle) also applies to `Rotation` and `Scale Handles`. 

![Rotation And Handles][rotation_scale_handle]

To add RotationHandle programmatically, use the following code
```
using Battlehub.RTHandles;
cube.AddComponent<RotationHandle>();
```

To add a ScaleHandle programmatically, use the following code:
```
using Battlehub.RTHandles;
capsue.AddComponent<ScaleHandle>(); 
```

# Scene Grid
The scene grid is represented by the `SceneGrid` component.

![Scene Grid][scene_grid]

# Scene Gizmo

The scene gizmo is represented by the `SceneGizmo` component.

![Scene Gizmo][scene_gizmo]

The Scene Gizmo can be positioned using the Pivot Point and Anchor properties. 
The `Pivot Point` property defines which corner of the Gizmo itself is used for alignment, while the `Anchor` property specifies the screen corner where the Scene Gizmo will be aligned. 
For example following are Pivot Point values:
- `(0, 0)` aligns the Gizmo's top-left corner to the chosen screen corner.
- `(1, 0)` aligns the top-right corner.
- `(1, 1)` aligns the bottom-right corner.


# Colors and Sizes

To customize the colors and sizes of the Runtime Transform Handles, SceneGrid, and SceneGizmo, use the `Runtime Handles Component`.

![Settings][settings]


# Scene Component

The Scene Component combines all transform handles and gizmos mentioned above, but it also enables scene navigation and selection. 
The navigation scheme is similar to those implemented in the Unity editor:

| Action      | Input                                                                 |
|-------------|-----------------------------------------------------------------------|
| Select      | Left-click                                                             |
| Move (Pan)  | Hold middle mouse button, then drag                                    |
| Flythrough  | Click and hold the right mouse button. Move around using mouse and WSAD keys |
| Orbit       | Hold Alt + left-click, then drag                                       |
| Zoom        | Use the scroll wheel                                                   |


To create a Scene Component, use the menu  **Tools -> Runtime Handles -> Create -> Scene Component.**

![Scene Component][scene_component]

The Scene Component takes control of the camera and does not allow it to be moved by external scripts using `Camera.transform`.

To move the scene camera controlled by the `RuntimeSceneComponent`, use the `SetCameraPositionAndPivot` method:

```
var sceneComponent = FindObjectOfType<RuntimeSceneComponent>();
sceneComponent.SetCameraPositionAndPivot(
            position: new Vector3(0, 10, 10),
            pivot:    new Vector3(0, 10, 0));
```

# Select Which Camera the Scene Component Will Control

By default, the `SceneComponent` tries to acquire control of the camera with the `MainCamera` tag (i.e., `Camera.main`).
If no such camera is found, a camera named `RTE SceneView Camera` is created and used as the scene camera.

To use your own camera, follow these steps:
- **Tools -> Runtime Handles -> Create -> RTE Component**.
- **Set the `Camera` field**.

![RTE Component][rte_component_set_camera]

# Scene Component (No Camera Controls)

To create a Scene Component that doesn't take control over the camera, 
use the menu 
- **Tools -> Runtime Handles -> Create -> Scene Component (NoCameraControls)**. 

This component still allows for the selection of GameObjects with the `ExposeToEditor` component, 
but it does not control the camera, allowing users to control the camera themselves.


# Select Current Transform Handle

To allow user selecting active transform handle using Q W E R T keys on keyboard create Tools Component using
**Tools -> Runtime Handles -> Create -> Tools Component.**

![Tools Component][tools_component]

To select the current tool programmatically, use the following code:

```csharp
using Battlehub.RTCommon;

var editor = IOC.Resolve<IRTE>();
editor.Tools.Current = RuntimeTool.Rotate;
```

**Pivot Mode**

- **Pivot**: Positions the Gizmo at the actual pivot point of a Mesh.
- **Center**: Positions the Gizmo at the center of the GameObject’s rendered bounds.

**Pivot Rotation**

- **Local**: Keeps the Gizmo’s rotation relative to the GameObject’s.
- **Global**: Clamps the Gizmo to world space orientation.

```csharp
editor.Tools.PivotMode = RuntimePivotMode.Center;
editor.Tools.PivotRotation = RuntimePivotRotation.Global;
```

# Runtime Undo

To enable undo and redo functionality using Shift (Ctrl) + Z and Shift (Ctrl) + Y, create the Undo Component:

**Tools -> Runtime Handles -> Create -> Undo Component.**

The reason for using Shift instead of Ctrl in the Unity editor is to avoid conflicts with Unity Editor's own undo/redo commands.

At runtime, the runtime editor will automatically use Ctrl + Z and Ctrl + Y.


# IOC
Assets/Battlehub/RTEditor/Runtime/RTCommon/Utils/**IOC.cs** is a simple IoC container implementation.

**Methods**:

- `static T Resolve<T>()`: Resolve a dependency of type 'T'.
- `static void Register<T>(Func<T> func)`: Register a construction function.
- `static void Register<T>(T instance)`: Register an instance.
- `static void Unregister<T>(Func<T> func)`: Unregister a construction function.
- `static void Unregister<T>(T instance)`: Unregister an instance.

**Example 1**:

```csharp
using UnityEngine;
using Battlehub.RTCommon;

public interface IDependency { }

public class Dependency : MonoBehaviour, IDependency
{
    void Awake()
    {
        IOC.Register<IDependency>(this);
    }

    void OnDestroy()
    {
        IOC.Unregister<IDependency>(this);
    }
}

public class User : MonoBehaviour
{
    void Start()
    {
        IDependency dependency = IOC.Resolve<IDependency>();
    }
}
```

**Example 2**:

```csharp
using UnityEngine;
using Battlehub.RTCommon;

[DefaultExecutionOrder(-1)]
public class Registrar : MonoBehaviour
{
    void Awake()
    {
        IOC.Register<IDependency>(() =>
        {
            GameObject go = new GameObject();
            return go.AddComponent<Dependency>();
        });
    }

    private void OnDestroy()
    {
        IOC.Unregister<IDependency>();
    }
}

public interface IDependency { }

public class Dependency : MonoBehaviour, IDependency
{
}

public class User : MonoBehaviour
{
    void Awake()
    {
        IDependency dependency = IOC.Resolve<IDependency>();
    }
}
```

# Runtime Selection

The `IRuntimeSelection` interface providing functionality to manage and interact with the selection of objects within the editor. Here is a detailed description of its properties and methods:

### Properties
- **Enabled**: Gets or sets a value indicating whether the selection functionality is enabled.
- **EnableUndo**: Gets or sets a value indicating whether undo functionality is enabled for selection changes.
- **activeGameObject**: Gets or sets the currently active selected GameObject.
- **activeObject**: Gets or sets the currently active selected object.
- **objects**: Gets or sets the array of currently selected objects.
- **gameObjects**: Gets an array of the currently selected GameObjects.
- **activeTransform**: Gets the Transform of the currently active selected GameObject.
- **Length**: Gets the number of currently selected objects.

### Events
- **SelectionChanged**: An event that is triggered when the selection changes. Subscribers to this event can perform actions in response to selection changes.

### Methods
- **IsSelected(Object obj)**: Determines whether a given object is currently selected.
- **Select(Object activeObject, Object[] selection)**: Selects the specified objects, with the specified object set as the active object.

This interface allows for robust selection management within the Runtime Editor, supporting multiple selection states, undo functionality, and event-driven responses to selection changes.

```csharp
using UnityEngine;
using Battlehub.RTCommon;

public class RuntimeSelectionExample : MonoBehaviour
{
    IRuntimeSelection m_selection;

    void Start()
    {
        var editor = IOC.Resolve<IRTE>();
        m_selection = editor.Selection;
		
		// Subscribe to the selection changed event
        m_selection.SelectionChanged += OnSelectionChanged;

		// Create a sample game object
        var go = GameObject.CreatePrimitive(PrimitiveType.Capsule);
        go.AddComponent<ExposeToEditor>();

		// Select the game object
        m_selection.activeObject = go;
    }

    void OnDestroy()
    {
        if (m_selection != null)
        {
            m_selection.SelectionChanged -= OnSelectionChanged;
        }
    }

    void OnSelectionChanged(Object[] unselectedObjects)
    {
        if (unselectedObjects != null)
        {
            for (int i = 0; i < unselectedObjects.Length; ++i)
            {
                Object unselected = unselectedObjects[i];
                Debug.Log("Unselected: " + unselected.name);
            }
        }

        if (m_selection.objects != null)
        {
            for (int i = 0; i < m_selection.objects.Length; ++i)
            {
                Object selected = m_selection.objects[i];
                Debug.Log("Selected: " + selected.name);
            }
        }
    }
}
```

# Runtime Tools

The RuntimeTools class allows you to track and manipulate the current tool, locking state, pivot modes, and other aspects of the editor's toolset.

**Properties**:

- **LockObject LockAxes**: Holds the aggregated locking state of selected objects.
- **UnityObject ActiveTool**: Reference to the active transform handle or gizmo. Active means that the user is currently interacting with it.
- **RuntimeTool Current**: The tool that is currently enabled for the Scene View:
  - None
  - Move
  - Rotate
  - Scale
  - View
  - Rect
  - Custom
- **RuntimePivotMode PivotMode**: Indicates whether we are in Center or Pivot mode.
- **RuntimePivotRotation PivotRotation**: Specifies the rotation of the tool handle:
  - Global
  - Local

**Events**:

- **event RuntimeToolsEvent ToolChanged**: Raised when the current tool changes.
- **event RuntimeToolsEvent PivotRotationChanged**: Raised when the pivot rotation changes.
- **event RuntimeToolsEvent PivotModeChanged**: Raised when the pivot mode changes.
- **event RuntimeToolsEvent LockAxesChanged**: Raised when the lock axes change.

**Additional Properties and Events**:

- **IsViewing**: Indicates whether the editor is in viewing mode. Changing this property raises the `IsViewingChanged` event.
- **AutoFocus**: Indicates whether auto-focus is enabled. Changing this property raises the `AutoFocusChanged` event.
- **UnitSnapping**: Indicates whether unit snapping is enabled. Changing this property raises the `UnitSnappingChanged` event.
- **IsSnapping**: Indicates whether snapping is enabled. Changing this property raises the `IsSnappingChanged` event.
- **SnappingMode**: Specifies the snapping mode. Changing this property raises the `SnappingModeChanged` event.
- **CustomPivotPosition**: Specifies a custom pivot position.
- **SelectionMode**: Specifies the selection mode. Changing this property raises the `SelectionModeChanged` event.
- **IsBoxSelectionEnabled**: Indicates whether box selection is enabled. Changing this property raises the `IsBoxSelectionEnabledChanged` event.

### Example Usage

**Changing the Current Tool**:

```csharp
using UnityEngine;
using Battlehub.RTCommon;

public class SwitchToolBehaviour : MonoBehaviour
{
    void Start()
    {
        IRTE editor = IOC.Resolve<IRTE>();
        editor.Tools.Current = RuntimeTool.Rotate;
    }
}
```

**Locking Axes for All Selected Objects**:

```csharp
using UnityEngine;
using Battlehub.RTCommon;

public class LockAxesForAllObjects : MonoBehaviour
{
    IRTE m_editor;
    void Start()
    {
        m_editor = IOC.Resolve<IRTE>();
        m_editor.Selection.SelectionChanged += OnSelectionChanged;
        m_editor.Tools.ToolChanged += OnToolChanged;
    }

    void OnDestroy()
    {
        if(m_editor != null)
        {
            m_editor.Selection.SelectionChanged -= OnSelectionChanged;
            m_editor.Tools.ToolChanged -= OnToolChanged;
        }
    }

    void OnToolChanged()
    {
        Lock();
    }

    void OnSelectionChanged(Object[] unselectedObjects)
    {
        Lock();
    }

    static void Lock()
    {
        IRTE editor = IOC.Resolve<IRTE>();
        editor.Tools.LockAxes = new LockObject
        {
            PositionY = true,
            RotationX = true,
            RotationZ = true
        };
    }
}
```

# Runtime Undo

The `IRuntimeUndo` interface is used to record changes, maintain the undo/redo stack, and perform undo and redo operations.

**Properties**:

- **bool Enabled { get; set; }**: Indicates whether undo and redo operations are enabled.
- **bool CanUndo { get; }**: Indicates whether an undo operation can be performed.
- **bool CanRedo { get; }**: Indicates whether a redo operation can be performed.
- **bool IsRecording { get; }**: Indicates whether multiple changes are being recorded.

**Methods**:

- **void BeginRecord()**: Begins recording multiple changes.
- **void EndRecord()**: Ends recording multiple changes.
- **void GroupRecords(int count = -1)**: Groups the specified number of records into a single undo/redo operation.
- **Record CreateRecord(UndoRedoCallback redoCallback, UndoRedoCallback undoCallback, PurgeCallback purgeCallback = null, EraseReferenceCallback eraseCallback = null)**: Creates a generic record.
- **Record CreateRecord(object target, object newState, object oldState, UndoRedoCallback redoCallback, UndoRedoCallback undoCallback, PurgeCallback purgeCallback = null, EraseReferenceCallback eraseCallback = null)**: Creates a record with a specific target, new state, and old state.
- **void RegisterCreatedObjects(ExposeToEditor[] createdObjects, Action afterRedo = null, Action afterUndo = null)**: Registers created objects.
- **void DestroyObjects(ExposeToEditor[] destroyedObjects, Action afterRedo = null, Action afterUndo = null)**: Registers destroy objects operation.
- **void BeginRecordValue(object target, MemberInfo memberInfo)**: Begins recording a value change.
- **void BeginRecordValue(object target, object accessor, MemberInfo memberInfo)**: Begins recording a value change with accessor.
- **void EndRecordValue(object target, MemberInfo memberInfo, Action afterRedo = null, Action afterUndo = null)**: Ends recording a value change.
- **void EndRecordValue(object target, object accessor, MemberInfo memberInfo, Action<object, object> targetErased = null, Action afterRedo = null, Action afterUndo = null)**: Ends recording a value change with accessor.
- **void BeginRecordTransform(Transform target, Action<Transform> afterUndo = null)**: Begins recording a transform change.
- **void EndRecordTransform(Transform target, Action<Transform> afterRedo = null)**: Ends recording a transform change.
- **void BeginRecordTransform(Transform target, Transform parent, int siblingIndex = -1, Action<Transform> afterUndo = null)**: Begins recording a transform change with parent and sibling index.
- **void EndRecordTransform(Transform target, Transform parent, int siblingIndex = -1, Action<Transform> afterRedo = null)**: Ends recording a transform change with parent and sibling index.
- **void AddComponent(ExposeToEditor obj, Type type)**: Adds a component and pushes the corresponding record to the stack.
- **void AddComponentWithRequirements(ExposeToEditor obj, Type type)**: Adds a component along with its required components.
- **void DestroyComponent(Component destroy, MemberInfo[] memberInfo)**: Destroys a component and pushes the corresponding record to the stack.
- **void Redo()**: Performs a redo operation.
- **void Undo()**: Performs an undo operation.
- **void Purge()**: Purges all records. All “marked as destroyed” objects will be destroyed.
- **void Erase(object oldRef, object newRef = null, bool ignoreLock = false)**: Replaces oldRef with newRef for all records in the stack.
- **void Store()**: Creates a new stack and stores the current undo and redo stack.
- **void Restore()**: Restores a previously stored stack.

**Events**:

- **event RuntimeUndoEventHandler BeforeUndo**: Raised before an undo operation.
- **event RuntimeUndoEventHandler UndoCompleted**: Raised after an undo operation.
- **event RuntimeUndoEventHandler BeforeRedo**: Raised before a redo operation.
- **event RuntimeUndoEventHandler RedoCompleted**: Raised after a redo operation.
- **event RuntimeUndoEventHandler StateChanged**: Raised whenever one of the following operations is performed:
  - Store
  - Restore
  - Purge

### Example Usage

**Recording a Value and then Undoing Changes**:

```csharp
using UnityEngine;
using System.Reflection;
using Battlehub.RTCommon;
using Battlehub.Utils;

public class RecordValueThenUndoChanges : MonoBehaviour
{
    IRuntimeUndo m_undo;

    [SerializeField]
    int m_value = 1;

    void Start()
    {
        m_undo = IOC.Resolve<IRTE>().Undo;
        m_undo.UndoCompleted += OnUndoCompleted;

        var valueInfo = Strong.MemberInfo((RecordValueThenUndoChanges x) => x.m_value);

        m_undo.BeginRecordValue(this, valueInfo);
        m_value = 2;
        m_undo.EndRecordValue(this, valueInfo);

        m_undo.Undo();
    }

    void OnDestroy()
    {
        if (m_undo != null)
        {
            m_undo.UndoCompleted -= OnUndoCompleted;
        }
    }

    void OnUndoCompleted()
    {
        Debug.Log(m_value); // 1
    }
}
```

### Mobile Scene (Touch Support)

Runtime Transform Handles have built-in touch support.

To see how to modify the `SceneComponent` to support scene navigation using touch input, refer to:

**Tools -> Runtime Handles -> Show Me Examples**  
`Battlehub/RTEEditorDemo/Component/Runtime/RTHandles`

The key difference with the standard `SceneComponent` is the usage of the `MobileSceneInput` script.

![Touch Support][touch_support]

### Demo Editor

The most advanced editor, which includes all the components and transform handles described in this document, along with UI and `PrefabSpawnPoints`, can be created by clicking:

**Tools -> Runtime Handles -> Create Demo Editor**

![Demo Editor][demo_editor]

**For more advanced scenarios—such as viewing your scene from multiple angles simultaneously, managing multiple windows, handling complex inspector windows,
and working with scene and prefab saving, loading, and runtime scripting—check out the [Runtime Editor Asset](https://assetstore.unity.com/packages/tools/modeling/runtime-editor-64806)**

## Support
If you cannot find something in the documentation or have any questions, please feel free to send an email to [Battlehub@outlook.com](mailto:Battlehub@outlook.com) or ask directly in [this support group](https://t.me/battlehub). You can also create an issue [here](https://github.com/BattlehubCode/RTH_Docs/issues) or [here](https://github.com/Battlehub0x/RTE_Docs/issues?q=is%3Aissue+is%3Aclosed). 
Keep up the great work in your development journey! 😊

<div style="page-break-after: always;"></div>

[youtube_icon]: 	 Docs/Images/overview.png
[getting_started_1]: Docs/Images/getting_started_1.png
[getting_started_2]: Docs/Images/getting_started_2.png
[getting_started_3]: Docs/Images/getting_started_3.jpg
[getting_started_4]: Docs/Images/getting_started_4.jpg
[getting_started_5]: Docs/Images/getting_started_5.jpg
[example_scenes_2]:  Docs/Images/example_scenes_2.jpg
[expose_to_editor]: Docs/Images/expose_to_editor.jpg
[universal_rp_1]:    Docs/Images/universal_rp_1.jpg
[universal_rp_2]:    Docs/Images/universal_rp_2.jpg
[universal_rp_3]:    Docs/Images/universal_rp_3.jpg
[universal_rp_4]:    Docs/Images/universal_rp_4.jpg
[universal_rp_5]:    Docs/Images/universal_rp_5.jpg
[create_menu]:       Docs/Images/create_menu.jpg
[position_handle]:    Docs/Images/position_handle.jpg
[position_handle_2]:    Docs/Images/position_handle_2.jpg
[rotation_scale_handle]:    Docs/Images/rotation_scale_handle.jpg
[scene_grid]:    Docs/Images/scene_grid.jpg
[scene_gizmo]:   Docs/Images/scene_gizmo.jpg
[settings]:   Docs/Images/settings.jpg
[scene_component]:   Docs/Images/scene_component.jpg
[tools_component]:   Docs/Images/tools_component.jpg
[rte_component_set_camera]:   Docs/Images/rte_component_set_camera.jpg
[touch_support]: Docs/Images/touch_support.jpg
[demo_editor]: Docs/Images/DemoEditor.jpg
