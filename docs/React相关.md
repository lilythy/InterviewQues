# React生命周期详解
## 一、初始化(initialization)阶段
执行constructor()构造方法，用来做一些组件的初始化工作，如定义this.state的初始内容。super(props)用来调用基类的构造方法( constructor() ), 也将父组件的props注入给子组件

## 二、挂载(Mounting)阶段
**此阶段分为componentWillMount，render，componentDidMount三个时期。**

- ** componentWillMount => UNSAFE_componentWillMount()（即将被废弃）:**
在组件挂载到DOM前调用，且只会被调用一次，在这边调用this.setState不会引起组件重新渲染，也可以把写在这边的内容提前到constructor()中，所以项目中很少用。

- **static getDerivedStateFromProps(props, state)(新)**
该生命周期在组件实例化以及接收新props后调用。它可以返回一个对象来更新state，或者返回null来表示新的props不需要任何state更新。这个新的生命周期可覆盖componentWillReceiveProps的所有用例。
setState也会调用该方法。

- **render组件渲染**
该方法会创建一个虚拟DOM，state或者props的更新或者componentShouldUpdate返回true都会引起render的重渲染，会多次执行。
> render方法返回的结果并不是真正的DOM元素，而是一个虚拟的表现，类似于一个DOM tree的结构的对象。react之所以效率高，就是这个原因。

- **componentDidMount**
组件渲染完成后调用该方法，只执行一次。在这里已经渲染出真实的dom节点了，可以再该方法中通过 this.getDOMNode() (新版本已被弃用，推荐使用ReactDOM.findDOMNode)访问到react DOM。
该方法中也可以调用setState来更新状态重新渲染组件，这里也是设置定时器监听事件的好地方。
> 上面说过组件并不是真实的dom节点，如果需要从组件获取真实 DOM 的节点，可以通过ref属性。

## 三、更新(update)阶段
- **componentWillReceiveProps(nextProps) => UNSAFE_componentWillReceiveProps(nextProps)（即将被废弃）**
只要父组件更新，不管props是否与以前相同，都会调用该方法。接收一个参数nextProps,所以在这里可以继续拿到this.props的值。在这个方法中更新state是安全的，一般情况下为了避免任何props的改变多次触发state更新，可以通过nextProps和this.props的比较后再做相关操作。

- **static getDerivedStateFromProps(props, state)(新)**
同上

- **shouldComponentUpdate(nextProps, nextState)**
通过返回true或者false来判断是否需要重新渲染组件。如果返回false，后面的render 以及 componentWillUpdate，componentDidUpdate 方法都将不会执行。组件比较多时，使用该方法能够避免不需要的重渲染，优化性能。

- **componentWillUpdate(nextProps, nextState) => UNSAFE_componentWillUpdate(nextProps, nextState)（即将被废弃）**
组件首次渲染后，如果再次收到state/props改变或者shouldComponentUpdate返回true,会调用该方法。在这里不能使用this.setState来修改状态。这个函数调用之后，就会把nextProps和nextState分别设置到this.props和this.state中。

- **render组件渲染**
同上

- **getSnapshotBeforeUpdate(nextProps, nextState)(新)**
render的输出结果渲染到dom之前调用。它的任何返回值都会作为参数传给componentDidUpdate。这个新的生命周期可覆盖componentWillUpdate的所有用例。

- **componentDidUpdate(nextProps, nextState, snapshot)**
类似于componentDidMount，在组件重新被渲染之后调用。可以在这里访问并修改 DOM。当使用getSnapshotBeforeUpdate返回值时，snapshot参数才会有值。

## 四、Unmounting 卸载阶段
componentWillUnmount
该方法将会在 component 从DOM中移除时调用。一些组件相关的清理工作都可以在这里处理。

# react有哪些性能提升的方法？
1. 只要是父组件中用了子组件，子组件就算没用prop也会进行依次渲染，
可以用pureComponent 或者 shouldComponentUpdate来控制渲染次数，
pureComponent 要注意它是浅比较，如果你的prop是对象，你直接修改对象的值是不会触发子组件渲染的，要改内存地址
2. 使用key来帮助React识别列表中所有子组件的最小变化。
3. 组件ref箭头函数提到construstor里，避免每次渲染都执行一遍
4. 定义事件时.bind(this) 在构造器constructor里绑定，这样只需绑定一次，不然每次都需要重新绑定。
5. redux性能优化：reselect（数据获取时优化）
既然react和redux都是通过数据驱动的的方式驱动渲染过程，那么处理优化渲染过程，获取数据的过程也是需要考虑的一个优化点。
```
//下面是redux中简单的一个筛选功能
const getVisibleTodos = (todos, filter) => {
		switch (filter) {
			case 'SHOW_ALL':
				return todos
			case 'SHOW_COMPLETED':
				return todos.filter(t => t.completed)
			case 'SHOW_ACTIVE':
				return todos.filter(t => !t.completed)
  		}
}
const mapStateToProps = (state) => {
	return {
		todos: getVisibleTodos(state.todos, state.visibilityFilter)
	}
}
```

mapStateToProps函数作为redux store中获取数据的重要一环，当我们根据filter和todos来显示相应的待办事项的时候，我们都要遍历todos字段上的数组。

当数组比较大的时候，则会降低性能。

这个时候，reselect就应运而生了，它的动作原理：只要相关的状态没有改变，那么就直接使用上一次的缓存结果。



