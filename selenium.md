### selenium使用clear事件不触发vue的双向数据绑定
- input中v-modal需要捆绑于input事件进行触发
- 引入js,手动在clear后进行事件派发
```js
function FireEvent(elem, eventName)
{
    if(typeof(elem) =='object')
    {
        eventName = eventName.replace(/^on/i,'');
        if (document.all)
        {//
            eventName ="on"+eventName;
            elem.fireEvent(eventName);
        }
        else
        {
            var evt = document.createEvent('HTMLEvents');
            evt.initEvent(eventName,true,true);
            elem.dispatchEvent(evt);
        }
    }
 
}
```
