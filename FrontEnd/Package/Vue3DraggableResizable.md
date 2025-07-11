### Props

| 属性名                | 类型      | 默认值                                               | 描述                                      | 示例                                                             |
| ------------------ | ------- | ------------------------------------------------- | --------------------------------------- | -------------------------------------------------------------- |
| initW              | Number  | null                                              | 设置初始宽度（px）                              | `<Vue3DraggableResizable :initW="100" />`                      |
| initH              | Number  | null                                              | 设置初始高度（px）                              | `<Vue3DraggableResizable :initH="100" />`                      |
| w                  | Number  | 0                                                 | 当前宽度（px），可使用 `v-model:w` 与父组件保持同步       | `<Vue3DraggableResizable v-model:w="100" />`                   |
| h                  | Number  | 0                                                 | 当前高度（px），可使用 `v-model:h` 与父组件保持同步       | `<Vue3DraggableResizable v-model:h="100" />`                   |
| x                  | Number  | 0                                                 | 距离父容器左侧的距离（px），可使用 `v-model:x` 与父组件保持同步 | `<Vue3DraggableResizable v-model:x="100" />`                   |
| y                  | Number  | 0                                                 | 距离父容器顶部的距离（px），可使用 `v-model:y` 与父组件保持同步 | `<Vue3DraggableResizable v-model:y="100" />`                   |
| minW               | Number  | 20                                                | 最小宽度（px）                                | `<Vue3DraggableResizable :minW="100" />`                       |
| minH               | Number  | 20                                                | 最小高度（px）                                | `<Vue3DraggableResizable :minH="100" />`                       |
| active             | Boolean | false                                             | 是否处于活跃状态，可使用 `v-model:active` 与父组件保持同步  | `<Vue3DraggableResizable v-model:active="true" />`             |
| draggable          | Boolean | true                                              | 是否可拖动                                   | `<Vue3DraggableResizable :draggable="false" />`                |
| resizable          | Boolean | true                                              | 是否可调整大小                                 | `<Vue3DraggableResizable :resizable="false" />`                |
| lockAspectRatio    | Boolean | false                                             | 是否锁定长宽比                                 | `<Vue3DraggableResizable :lockAspectRatio="true" />`           |
| disabledX          | Boolean | false                                             | 是否禁止在 X 轴上移动                            | `<Vue3DraggableResizable :disabledX="true" />`                 |
| disabledY          | Boolean | false                                             | 是否禁止在 Y 轴上移动                            | `<Vue3DraggableResizable :disabledY="true" />`                 |
| disabledW          | Boolean | false                                             | 是否禁止修改宽度                                | `<Vue3DraggableResizable :disabledW="true" />`                 |
| disabledH          | Boolean | false                                             | 是否禁止修改高度                                | `<Vue3DraggableResizable :disabledH="true" />`                 |
| parent             | Boolean | false                                             | 是否将拖动和缩放限制在父节点内                         | `<Vue3DraggableResizable :parent="true" />`                    |
| handles            | Array   | \['tl', 'tm', 'tr', 'ml', 'mr', 'bl', 'bm', 'br'] | 定义缩放的句柄方向                               | `<Vue3DraggableResizable :handles="['tl','tr','bl','br']" />`  |
| classNameDraggable | String  | 'draggable'                                       | 可拖动时的自定义类名                              | `<Vue3DraggableResizable classNameDraggable="my-draggable" />` |
| classNameResizable | String  | 'resizable'                                       | 可缩放时的自定义类名                              | `<Vue3DraggableResizable classNameResizable="my-resizable" />` |
| classNameDragging  | String  | 'dragging'                                        | 拖动时的自定义类名                               | `<Vue3DraggableResizable classNameDragging="my-dragging" />`   |
| classNameResizing  | String  | 'resizing'                                        | 缩放时的自定义类名                               | `<Vue3DraggableResizable classNameResizing="my-resizing" />`   |
| classNameActive    | String  | 'active'                                          | 活跃状态下的自定义类名                             | `<Vue3DraggableResizable classNameActive="my-active" />`       |
### Event

| 事件名         | 描述          | 回调参数                   |
| ----------- | ----------- | ---------------------- |
| dragging    | 拖拽时触发       | `x, y`：拖拽后的坐标          |
| resizing    | 缩放时触发       | `x, y, w, h`：缩放后的坐标和尺寸 |
| activated   | 活跃状态激活时触发   | 无                      |
| deactivated | 活跃状态取消激活时触发 | 无                      |