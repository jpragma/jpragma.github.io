---
layout: post
title: Building Reactive Communication between Parent and Child Components in React
date: 2023-12-20 21:00
author: isaaclevin
comments: true
categories: [React]
---
In React applications, the construction of user interfaces often involves assembling custom components. 
While the common practice for a child component to communicate with its parent is through the firing of custom events, 
there are scenarios where a more imperative approach is necessary. This is particularly true when dealing with third-party 
widgets lacking convenient events or when the computation of state required by the parent is resource-intensive and 
should be performed only when explicitly requested by the parent. Achieving this functionality involves leveraging two key concepts: 
[forwardRef](https://react.dev/reference/react/forwardRef) and [useImperativeHandle](https://react.dev/reference/react/useImperativeHandle).

Consider the implementation of a child component utilizing these concepts:

{% highlight javascript %}
const ChildComponent = ({
    param1,
    param2
}, ref) => {
    const [foo, setFoo] = useState(null)

    useImperativeHandle(ref, () => ({
        myFunction: () => {
            ...
            return someComplexAlgoResult()
        }
    }))

    useEffect(() => {
        loadInitData()
    }, [])

    return (
        <div>content... {foo}</div>
    )
}
export default forwardRef(ChildComponent)
{% endhighlight %}

In the code snippet above, the **forwardRef** function is employed to forward the **ref** parameter from the parent to the child component. 
Additionally, **useImperativeHandle** is utilized to expose a function (**myFunction** in this case) from the child component to the parent, 
allowing imperative calls.

Now, let's demonstrate how to use this child component in a parent component:

{% highlight javascript %}
const ParentComponent = () => {
    const childComponentRef = useRef(null)
    
    function onSave() {
        const dataFromChild = childComponentRef.current.myFunction()
        ...
    }

    return (
        <ChildComponent ref={childComponentRef}/>
    )
}
{% endhighlight %}

In the parent component, a **useRef** is employed to create a mutable object (**childComponentRef**), 
which is then passed to the **ref** attribute of the **ChildComponent**. This enables the parent to call the exposed function (**myFunction**) 
imperatively when needed, providing a seamless way for the parent to interact with the child component's internal logic.