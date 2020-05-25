# DragableArea

> A dragable component for vue.

## 配置

| name           | type   | default | description                 |
| -------------- | ------ | ------- | --------------------------- |
| distanceRight  | number | 0       | 元素距右侧初始距离          |
| distanceBottom | number | 100     | 元素距底部初始距离          |
| gutterX        | number | 50      | 元素移动终点距 X 轴最小距离 |
| gutterY        | number | 100     | 元素移动终点距 Y 轴最小距离 |
| zIndex         | number | 50      | 默认容器渲染层级            |

## 功能

- 支持拖拽
- 支持设置初始位置
- 支持 PC 端
- 支持移动端 touch 事件
- 支持 Drag 事件（兼容性不好）
