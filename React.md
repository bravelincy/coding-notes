# React学习笔记

[TOC]

> React官方的简介是它是一个用于构建用户界面的Javascript库，具有以下特点：
- **声明式**   
以一种无痛的方式创建可交互的UI，数据的变化驱动相关组件的状态更新，声明式的界面让调试变得更加简单。
- **基于组件**  
构建只管理自身状态的封装组件，然后组合它们就可以构建出复杂的UI。因为组件的逻辑写在JS中而不是模板中，你可以更加轻松地传入数据到App中，不用通过DOM就可以保持状态。
- **一旦学会，到哪都能用**  
我们并没有假设你应用其他的部分用了什么技术栈，所以你可以立即使用React而不用重写你现有的代码。它还可以通过Node在服务端渲染，而且可以用React Native来构建移动App。

## 一个简单的例子
React组件实现了一个`render()`方法，它可以拿到输入的数据然后返回要展示的内容。这个例子用了一种类似XML的语法它叫JSX，传入组件的数据可以在`render()`方法内通过`this.props`获取到。

JSX是可选的，React没有限制你一定要使用它。JSX的语法会被编译成普通的JS代码。

JSX语法：
``` javascript
class HelloMessage extends React.Component {
    render() {
        return <div>Hello {this.props.name}</div>
    }
}

ReactDOM.render(<HelloMessage name="Jane"/>, mountNode)
```

普通JS语法：
``` javascript
class HelloMessage extends React.Component {
    render() {
        return React.createElement(
            'div',
            null,
            'Hello ',
            this.props.name
        )
    }
}

ReactDOM.render(
    React.createElement(HelloMessage, {name: 'Jane'}),
    mountNode
)
```

## 一个带有状态的组件
除了通过`this.props`获取输入数据之外，一个组件可以通过`this.state`来管理组件内部的状态数据。当一个组件的状态数据发生变化，已经渲染的界面会通过重新执行`render()`方法来更新。

JSX语法：
``` javascript
class Timer extends React.Component {
    constructor(props) {
        super(props)
        this.state = {secondsElapsed: 0}
    }
    
    tick() {
        this.setState(prevState => ({
            secondsElapsed: prevState.secondsElapsed + 1
        }))
    }
    
    componentDidMount() {
        this.interval = setInterval(() => this.tick(), 1000)
    }
    
    componentWillUnmount() {
        clearInterval(this.interval)
    }
    
    render() {
        return (
            <div>Seconds Elapsed: {this.state.secondsElapsed}</div>
        )
    }
}

ReactDOM.render(<Timer/>, mountNode)
```

普通JS语法：
``` javascript
class Timer extends React.Component {
    constructor(props) {
        super(props)
        this.state = {secondsElapsed: 0}
    }
    
    tick() {
        this.setState(prevState => ({
            secondsElapsed: prevState.secondsElapsed + 1
        }))
    }
    
    componentDidMount() {
        this.interval = setInterval(() => this.tick(), 1000)
    }
    
    componentWillUnmount() {
        clearInterval(this.interval)
    }
    
    render() {
        return React.createElement(
            'div',
            null,
            'Seconds Elapsed: ',
            this.state.secondsElapsed
        )
    }
}

ReactDOM.render(
    React.createElement(Timer, null),
    mountNode
)
```

## 一个应用
使用`props`和`state`，我们可以结合起来做一个TODO小应用。这个例子使用`state`来跟踪当前列表的任务项和用户输入的文字。尽管事件处理程序写在行内，但它们会被收集起来并且通过事件代理来实现。

JSX语法：
``` javascript
class TodoApp extends React.Component {
    constructor(props) {
        super(props)
        this.handleChange = this.handleChange.bind(this)
        this.handleSubmit = this.handleSubmit.bind(this)
        this.state = {
            items: [],
            text: ''
        }
    }
    
    render() {
        return (
            <div>
                <h3>TODO</h3>
                <TodoList items={this.state.items}/>
                <form onSubmit={this.handleSubmit}>
                    <input onChange={this.handleChange} value={this.state.text}/>
                    <button>{'Add #' + this.state.items.length + 1}</button>
                </form>
            </div>
        )
    }
    
    handleChange(e) {
        this.setState({text: e.target.value})
    }
    
    handleSubmit(e) {
        e.preventDefault()

        var newItem = {
            text: this.state.text,
            id: Date.now()
        }
        
        this.setState(prevState => ({
            items: prevState.items.concat(newItem),
            text: ''
        }))
    }
}

class TodoList extends React.Component {
    render() {
        return (
            <ul>
                {this.state.items.map(item => (
                    <li key={item.id}>{item.text}</li>
                ))}
            </ul>
        )
    }
}

ReactDOM.render(<TodoApp>, mountNode)
```

普通JS语法：
``` javascript
class TodoApp extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.state = { items: [], text: '' };
  }

  render() {
    return React.createElement(
      'div',
      null,
      React.createElement(
        'h3',
        null,
        'TODO'
      ),
      React.createElement(TodoList, { items: this.state.items }),
      React.createElement(
        'form',
        { onSubmit: this.handleSubmit },
        React.createElement('input', { onChange: this.handleChange, value: this.state.text }),
        React.createElement(
          'button',
          null,
          'Add #' + (this.state.items.length + 1)
        )
      )
    );
  }

  handleChange(e) {
    this.setState({ text: e.target.value });
  }

  handleSubmit(e) {
    e.preventDefault();
    var newItem = {
      text: this.state.text,
      id: Date.now()
    };
    this.setState(prevState => ({
      items: prevState.items.concat(newItem),
      text: ''
    }));
  }
}

class TodoList extends React.Component {
  render() {
    return React.createElement(
      'ul',
      null,
      this.props.items.map(item => React.createElement(
        'li',
        { key: item.id },
        item.text
      ))
    );
  }
}

ReactDOM.render(React.createElement(TodoApp, null), mountNode);
```

## 一个使用外部插件的组件
React很灵活，它提供了一些钩子使得你可以连接其他库和框架。这个例子使用了一个叫**remarkable**的Markdown外部插件，用来实时转换过文本框中的值。

JSX语法：
``` javascript
class MarkdownEditor extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = {value: 'Type some *markdown* here!'};
  }

  handleChange() {
    this.setState({value: this.refs.textarea.value});
  }

  getRawMarkup() {
    var md = new Remarkable();
    return { __html: md.render(this.state.value) };
  }

  render() {
    return (
      <div className="MarkdownEditor">
        <h3>Input</h3>
        <textarea
          onChange={this.handleChange}
          ref="textarea"
          defaultValue={this.state.value} />
        <h3>Output</h3>
        <div
          className="content"
          dangerouslySetInnerHTML={this.getRawMarkup()}
        />
      </div>
    );
  }
}

ReactDOM.render(<MarkdownEditor />, mountNode);
```

普通JS语法：
``` javascript
class MarkdownEditor extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = { value: 'Type some *markdown* here!' };
  }

  handleChange() {
    this.setState({ value: this.refs.textarea.value });
  }

  getRawMarkup() {
    var md = new Remarkable();
    return { __html: md.render(this.state.value) };
  }

  render() {
    return React.createElement(
      "div",
      { className: "MarkdownEditor" },
      React.createElement(
        "h3",
        null,
        "Input"
      ),
      React.createElement("textarea", {
        onChange: this.handleChange,
        ref: "textarea",
        defaultValue: this.state.value }),
      React.createElement(
        "h3",
        null,
        "Output"
      ),
      React.createElement("div", {
        className: "content",
        dangerouslySetInnerHTML: this.getRawMarkup()
      })
    );
  }
}

ReactDOM.render(React.createElement(MarkdownEditor, null), mountNode);
```