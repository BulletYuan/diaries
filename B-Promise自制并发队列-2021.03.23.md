这是很久之前的一个项目上的需求，需要在同一时间段内发起多个请求。应用场景多见于批量上传，批量处理等。

=======

于是乎首先想到了`Promise`，首先可以解决异步等待请求加载的问题，其次需要使用到`Promise.all`和`Promise.race`分别去执行列表内的异步函数。
后面随着场景越来越多变，发觉列表内不仅会有异步函数，可能还会有其他类型的值。
于是乎就来修改一下整个函数体的处理逻辑。


```javascript
async function queue(pms = [], limit = 3) {
    let seq = [];
    const result = [];
    if (!pms || pms.length === 0) return undefined;
    if (pms.length < limit) limit = pms.length;
    pms.forEach(p => {
        if (p instanceof Promise) {
            seq.push(p)
        } else {
            result.push(p)
        }
    })
    return new Promise(res => {
        if (seq.length > 0) {
            if (seq.length < limit) limit = seq.length;
            const que = seq.slice(0, limit).map(async (fn, i) => {
                const r = await fn;
                result.push(r)
                return i;
            })
            seq.reduce(async (last, cur, i) => {
                const c = await cur;
                result.push(c);
                return last.then(() => {
                    return Promise.race(que);
                })
                    .catch(err => {
                        console.error(err);
                    })
                    .then(res => {
                        que[res] = seq[i]
                    })
            }, Promise.resolve())
            .then(async () => {
                await Promise.all(que)
                res(result)
            })
        } else {
            res(result)
        }
    })
}
```

2021.07.08 增加有序返回最终数据列表（保持原有数组顺序）

```javascript
const queue = async (pms = [], limit = 3, ordering = false) => {
    const tempSeq = [];
    const result = [];
    if (!pms || pms.length === 0) return undefined;
    if (pms.length < limit) limit = pms.length;
    pms.forEach((p, i) => {
      if (p instanceof Promise) {
        ordering ?
          tempSeq[i] = p : tempSeq.push(p)
      } else {
        ordering ?
          result[i] = p : result.push(p)
      }
    })
    const seq = tempSeq.reduce((l, c, index) => c ? l.concat([{ fn: c, index }]) : l, [])
    return new Promise(res => {
      if (seq.length > 0) {
        if (seq.length < limit) limit = seq.length;
        const que = seq.slice(0, limit).map(async (obj, i) => {
          const { fn, index } = obj
          const r = await fn;
          ordering ?
            result[index] = r : result.push(r)
          return i;
        })
        seq.reduce(async (last, cur, i) => {
          const { fn, index } = cur
          const c = await fn;
          ordering ?
            result[index] = c : result.push(c)
          return last.then(() => {
            return Promise.race(que);
          })
            .catch(err => {
              console.error(err);
            })
            .then(res => {
              que[res] = seq[i]
            })
        }, Promise.resolve())
          .then(async () => {
            await Promise.all(que)
            res(result)
          })
      } else {
        res(result)
      }
    })
  }
```
