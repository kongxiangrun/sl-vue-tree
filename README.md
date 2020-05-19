# sl-vue-tree

Customizable draggable tree component for Vue.js

![preview](preview.png)

[demo](https://holiber.github.io/sl-vue-tree/demo/index)

install

`npm i sl-vue-tree`


	
# Quick start
````html

<div id="app">
  <sl-vue-tree v-model="nodes"/>
</div>

<link rel="stylesheet" href="dist/sl-vue-tree-dark.css">
<script src="dist/sl-vue-tree.js"></script>

<script>

  var nodes = [
    {title: 'Item1', isLeaf: true},
    {title: 'Item2', isLeaf: true, data: { visible: false }},
    {title: 'Folder1'},
    {
      title: 'Folder2', isExpanded: true, children: [
        {title: 'Item3', isLeaf: true},
        {title: 'Item4', isLeaf: true}
      ]
    }
  ];

  new Vue({
    el: '#app',
    components: { SlVueTree },
    data: function () {
     return {
       nodes: nodes
     }
    }
  });
  
</script>    

````

The `value` property is an array of `ISlTreeNodeModel` nodes:

````typescript
interface ISlTreeNodeModel<TDataType> {
    title: string;
    isLeaf?: boolean;
    children?: ISlTreeNodeModel<TDataType>[];
    isExpanded?: boolean;
    isSelected?: boolean;
    isDraggable?: boolean;
    isSelectable?: boolean;
    data?: TDataType; // any serializable user data
}
````

For convenience the component's events return `ISlTreeNode` objects. Those actually are `ISlTreeNodeModel`
with some computed props:
````typescript
interface ISlTreeNode<TDataType> extends ISlTreeNodeModel<TDataType> {
    isFirstChild: boolean;
    isLastChild: boolean;
    isVisible: boolean;	// node is visible if all of its parents are expanded
    level: number; // nesting level
    ind: number; // index in the array of siblings 
    path: number[]; // path to node as array of indexes, for example [2, 0, 1] in the example above is path to `Item4` 
    pathStr: string; // serialized path to node
    children: ISlTreeNode<TDataType>[];
}
````

You can get the list of `ISlTreeNode` from the computed `slVueTree.nodes` property



# Props

| prop             | type               | default                | description                                                                                                                                                                                              |
|------------------|--------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| value            | ISlTreeNodeModel[] | []                     | An array of nodes to show. Each node is represented by `ISlTreeNodeModel` interface 要显示的节点数组。 每个节点由“ ISlTreeNodeModel”接口表示                                                                                                                              |
| allowMultiselect | Boolean            | true                   | Disable or enable the multiselect feature 禁用或启用多选功能                                                                                                                                                               |
| allowToggleBranch | Boolean            | true                   | Disable or enable the expand/collapse button  禁用或启用展开/折叠按钮                                                                                                                                                              |
| edgeSize         | Number             | 3                      | Offset in pixels from top and bottom for folder-node element. While dragging cursor is in that offset, the dragging node will be placed before or after the folder-node instead of being placed inside the folder. 文件夹节点元素的上下偏移量（以像素为单位）。 当拖动光标处于该偏移量时，拖动节点将放置在文件夹节点之前或之后，而不是放置在文件夹内部。|
| scrollAreaHeight | Number             | 70                     | Offset in pixels from top and bottom for the component element. While dragging cursor is in that area, the scrolling starts. 组件元素的上下偏移量。 当拖动光标在该区域中时，开始滚动。                                                                               |
| maxScrollSpeed   | Number             | 20                     | The scroll speed is relative to the cursor position. Defines the max scroll speed. 滚动速度是相对于光标位置的。 定义最大滚动速度。
| multiselectKey   | String/String[] |['ctrlKey', 'metaKey'] | The keys for multiselect mode. Allowed values are 多选模式的键。 允许值为 ['ctrlKey', 'metaKey', 'altKey']   

# Computed props

| prop           | type            | description                                                                                                                                                                                                                                                     |
|----------------|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nodes          | ISlTreeNode[]   | List of nodes with some computed props. See `ISlTreeNode` interface.    具有一些计算道具的节点列表。 参见`ISlTreeNode`接口。                                                                                                                                                                                        |
| cursorPosition | ICursorPosition | Represents the current cursor position that describes the action that will be applied to the dragged node on `mouseup` event. See `ICursorPosition` interface 表示当前光标位置，该位置描述将在`mouseup`事件上应用于拖动节点的动作。 参见“光标位置”界面 |
| selectionSize  | Number          | The count of selected nodes 所选节点数
| dragSize       | Number          | The count of selected and draggable nodes 选定节点和可拖动节点的数量
| isDragging     | Boolean         | True if nodes are dragging 如果节点正在拖动，则为真

````
interface ICursorPosition<TDataType> {
  node: ISlTreeNode<TDataType>;
  placement: 'before' | 'inside' | 'after';
}  
````

# Events

| event           | callback arguments                                                         | description                                       |
|-----------------|----------------------------------------------------------------------------|---------------------------------------------------|
| input           | nodes: ISlTreeNodeModel[]                                                  | triggers for any changes to `value` property     |
| select          | selectedNodes: ISlTreeNode[], event: MouseEvent                            | triggers when a node has been selected/deselected |
| toggle          | toggledNode: ISlTreeNode, event: MouseEvent                                | triggers when a node has been collapsed/expanded  |
| drop            | draggingNodes: ISlTreeNode[], position: ICursorPosition, event: MouseEvent | triggers when dragging nodes have been dropped    |
| nodeclick       | node: ISlTreeNode, event: MouseEvent                                       | handle `click` event on node                      |
| nodedblclick    | node: ISlTreeNode, event: MouseEvent                                       | handle `dblclick` event on node                   |
| nodecontextmenu | node: ISlTreeNode, event: MouseEvent                                       | handle `contextmenu` event on node                |
| externaldrop    | cursorPosition: ICursorPosition, event: MouseEvent                         | handle `drop` event for external items [demo](https://holiber.github.io/sl-vue-tree/demo/externaldrag)             |

# Methods


| method                                                                                                   | description                                                                                        |
|----------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| getNode(path: number[]): ISlTreeNode                                                                     | Find the node by using its path                                                                   |
| traverse(cb: (node: ISlTreeNode, nodeModel: ISlTreeNodeModel, siblings: ISlTreeNodeModel[])  => boolean) | Helpful method to traverse all nodes. The traversing will be stopped if callback returns `false`.  |
| updateNode(path: number[], patch: Partial<ISlTreeNodeModel>)                                             | Update the node by using its path                                                                 |
| select(path: number[], addToSelection = false)                                                           | Select the node by using its path                                                                 |                                                                                                                                              |
| getNodeEl(): HTMLElement                                                                                 | Get the node HTMLElement by using its path                                                        |
| getSelected(): ISlTreeNode[]                                                                             | Get selected nodes                                                                                 |
| insert(position: ICursorPosition, nodeModel: ISlTreeNodeModel)                                           | Insert nodes by the current cursor position.                                                       |
| remove(paths: number[][])                                                                                | Remove nodes by paths. For example `.remove([[0,1], [0,2]])`
| getFirstNode(): ISlTreeNode                                                                              | Get the first node in the tree                                                                     |
| getLastNode(): ISlTreeNode                                                                               | Get the last node in the tree
| getNextNode(path: number[], filter?: (node: ISlTreeNode<TDataType>) => boolean): ISlTreeNode<TDataType>; | Get the next node. You can skip the next nodes by using `filter`
| getPrevNode(path: number[], filter?: (node: ISlTreeNode<TDataType>) => boolean): ISlTreeNode<TDataType>; | Get the previous node. You can skip the previous nodes by using `filter`

# Slots


| slot       | context     | description                                                                                   |
|------------|-------------|-----------------------------------------------------------------------------------------------|
| title      | ISlTreeNode | Slot for item title                                                                           |
| toggle     | ISlTreeNode | Slot for expand/collapse button                                                               |
| sidebar    | ISlTreeNode | Sidebar content                                                                               |
| draginfo   | SlVueTree   | Slot that follows the mouse cursor while dragging. By default shows the dragging nodes count. |
| empty-node | ISlTreeNode | Slot for (optional) message when folder is open, but empty                                    |

# IE 11 support

You must add a [babel-polyfill](https://babeljs.io/docs/en/babel-polyfill) for it to work correctly in IE11  
[See IE11 example](https://holiber.github.io/sl-vue-tree/demo/ie11test.html)


# Examples


## Add a folder or item icon via `title` slot
````html
<sl-vue-tree v-model="nodes">
    <template slot="title" slot-scope="{ node }">

      <span class="item-icon">
        <i class="fa fa-file" v-if="node.isLeaf"></i>
        <i class="fa fa-folder" v-if="!node.isLeaf"></i>
      </span>
    
      {{ node.title }}
      
    </template>
</sl-vue-tree>

````

## Select all nodes

```javascript
slVueTree.traverse((node, nodeModel, path) => {
    Vue.set(nodeModel, 'isSelected', true);
})
```

## Handle keydown and keyup events via `getNextNode` and `getPrevNode` methods

[demo](https://holiber.github.io/sl-vue-tree/demo/keyboardcontrol)

## Contributing
[see CONTRIBUTING.md](CONTRIBUTING.md)

# Changelog
v1.8.5
- fix d.ts https://github.com/holiber/sl-vue-tree/pull/77

v1.8.4
- added `insert` method https://github.com/holiber/sl-vue-tree/pull/39

v1.8.3
- Allow to disable or enable the expand/collapse button https://github.com/holiber/sl-vue-tree/pull/33

v1.8.1
- added IE11 support https://github.com/holiber/sl-vue-tree/issues/17

v1.8.0

- added `empty-node` slot

v1.7.1

- added `multiselectKey` property

v1.7.0

- added `isSelectable` and `isDraggable` flags

v1.6.0

- added `getNextNode` and `getPrevNode` methods https://github.com/holiber/sl-vue-tree/issues/6

v1.5.1
- improve drop on the bottom of tree https://github.com/holiber/sl-vue-tree/issues/5

v1.5.0
- `SlVueTree.select` method added
- `SlVueTree.@nodeclick` event fixed
