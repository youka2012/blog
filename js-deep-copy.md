##引言

搜索JavaScript对象的深度拷贝，往往会冒出JSON转换和递归拷贝大法。但遇到大数据量，它们都有调用栈爆栈的风险
今天，我们尝试利用树的利用深度/广度优先遍历来实现对象的深度拷贝。以下代码在chrome环境下全部测试通过。
(待完善Array,Symbol值类型)

## 深度优先遍历实现对象的深度拷贝
 

深度优先遍历对象，利用栈做中间节点缓存
         function deepCopy(orginObject) {
        if (orginObject == null || typeof orginObject !== 'object') {
            return;
        }
        console.log('starting')
        const resultObject = {}
        const rootKey = Symbol('root');
        //深度遍历需要创建栈
        const stack = [];
        for (let key of Object.keys(orginObject)) {
            stack.push({
                key: key,//属性名
                value: orginObject[key],//value属性记录当前节点下属数据
                parent: resultObject//记录节点在resultObject中的位置
            })
        }
    
        while (stack.length) {
            const currentNode = stack.pop();
            const parent = currentNode.parent;
            const currentKey = currentNode.key;
            const currentValue = currentNode.value;
            //若是无下属对象，返回其值
            if (currentValue == null || typeof currentValue !== 'object') {
                parent[currentKey] = currentValue;
            } else {
                //若下属值是对象，将子节点压入栈中
                parent[currentKey] = Array.prototype.toString.call(currentValue) === '[object Array]'?[]:{};
                for (let key of Object.keys(currentValue)) {
                    console.log('loop:' + key, currentValue[key])
                    stack.push({
                        key: key,
                        value: currentValue[key],
                        parent: parent[currentKey]
                    })
                }
            }
        }
        return resultObject;
    }
    var copyObj = deepCopy({ a: { b: 1, d: { e: 6 } }, c: 3 });
    console.log(copyObj);


## 广度优先遍历实现对象的深度拷贝

广度优先遍历对象，利用队列做中间节点缓存
    
        function deepCopy(orginObject) {
        if (orginObject == null || typeof orginObject !== 'object') {
            return;
        }
        console.log('starting')
        const resultObject = {}
        const rootKey = Symbol('root');
        //深度遍历需要创建栈
        const queue = [];
        for (let key of Object.keys(orginObject)) {
            queue.push({
                key: key,//属性名
                value: orginObject[key],//value属性记录当前节点下属数据
                parent: resultObject//记录节点在resultObject中的位置
            })
        }
    
        while (queue.length) {
            const currentNode = queue.shift();
            const parent = currentNode.parent;
            const currentKey = currentNode.key;
            const currentValue = currentNode.value;
            //若是无下属对象，返回其值
            if (currentValue == null || typeof currentValue !== 'object') {
                parent[currentKey] = currentValue;
            } else {
                //若下属值是对象，将子节点压入栈中
                parent[currentKey] = Array.prototype.toString.call(currentValue) === '[object Array]'?[]:{};
                for (let key of Object.keys(currentValue)) {
                    console.log('loop:' + key, currentValue[key])
                    queue.push({
                        key: key,
                        value: currentValue[key],
                        parent: parent[currentKey]
                    })
                }
            }
        }
        return resultObject;
    }
    var copyObj = deepCopy({ a: { b: 1, d: { e: 6 } }, c: 3 });
    console.log(copyObj);
