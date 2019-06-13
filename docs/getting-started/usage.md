---
id: usage
title: Usage
---

Here are some brief docs to give you a quick overview on all the features in Fluid Behavior Tree.

## Your First Tree

When creating trees you'll need to store them in a variable to properly cache all the necessary data.

```C#
using UnityEngine;
using CleverCrow.Fluid.BTs.Tasks;
using CleverCrow.Fluid.BTs.Trees;

public class MyCustomAi : MonoBehaviour {
    [SerializeField]
    private BehaviorTree _tree;
    
    private void Awake () {
        _tree = new BehaviorTreeBuilder(gameObject)
            .Sequence()
                .Condition("Custom Condition", () => {
                    return true;
                })
                .Do("Custom Action", () => {
                    return TaskStatus.Success;
                })
            .End()
            .Build();
    }

    private void Update () {
        // Update our tree every frame
        _tree.Tick();
    }
}
```

## What a Returned TaskStatus Does

Depending on what you return for a task status different things will happen.

* Success: Node has finished, next `tree.Tick()` will restart the tree if no other nodes to run
* Failure: Same as success, except informs that the node failed
* Continue: Rerun this node the next time `tree.Tick()` is called. A pointer reference is tracked by the tree and can only be cleared if `tree.Reset()` is called.

## Tree Visualizer

As long as your tree storage variable is set to `public` or has a `SerializeField` attribute. You'll be able to print a visualization of your tree while the game is running in the editor. Note that you cannot view trees while the game is not running. As the tree has to be built in order to be visualized.

![Visualizer](assets/tree-visualizer.png)

## Extending Trees

You can safely add new code to your behavior trees with several lines. Allowing you to customize BTs while supporting future version upgrades. 

```c#
using UnityEngine;
using CleverCrow.Fluid.BTs.Tasks;
using CleverCrow.Fluid.BTs.Tasks.Actions;
using CleverCrow.Fluid.BTs.Trees;

public class CustomAction : ActionBase {
    protected override TaskStatus OnUpdate () {
        Debug.Log(Owner.name);
        return TaskStatus.Success;
    }
}

public static class BehaviorTreeBuilderExtensions {
    public static BehaviorTreeBuilder CustomAction (this BehaviorTreeBuilder builder, string name = "My Action") {
        return builder.AddNode(new CustomAction { Name = name });
    }
}

public class ExampleUsage : MonoBehaviour {
    public void Awake () {
        var bt = new BehaviorTreeBuilder(gameObject)
            .Sequence()
                .CustomAction()
            .End();
    }
}
```
