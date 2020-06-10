# HTML string parsing

这几天因为自己的nodejs爬虫程序,解析html内容不力,感到烦躁.

npm库里面有个 `jsdom` 的第三方库,但是我打算还是试试自己来实现一下解析过程.

目前的大致想法是: 将html标签用对象的方式去抽象化.

目前的做法有两种:

* 把每个元素标签识别到放在一个数组里面 (开闭标签), 然后通过逻辑去判断 `attr` 和 `innerHTML` 两个内容.

* 用正则匹配父级标签, 然后递归匹配子集标签, 而后将 `attr` 和 `innerHTML` 取出.

```javascript
function a(html){
  let [tagStart,tagEnd,tag]=[false,false,''];
  const tree=[];
  let exgex=[];
  if(html&&html.length>0){
    chd=html.match(/<[^>]*>([^<|^>|^\/]*?)<\/[^>]*>/gis)||[];
    prt=html.match(/<.*[\/]*?>/gis)||[];
    for(let i =0;i<html.length;i++){
      const char=html[i];
      if(char!=='>'&&tagStart){
        if(char==='/'){
            tagEnd=false;
        }else{
          tag+=char;
        }
      }else if(char==='>'){
        tagStart=false;
        tree.push(tag);
        tagEnd=true;
      }else if(char==='<'){
        tag='';
        tagStart=true;
      }
    }
    console.log('chd',chd)
    console.log('prt',prt)
    console.log('tree',tree)
  }
}
a(`<div class="123"><p>123</p><input type="text" /></div><div class="1"><a href="#"></a></div>`)

// chd (3) ["<p>123</p>", "<input type="text" /></div>", "<a href="#"></a>"]
// prt ["<div class="123"><p>123</p><input type="text" /></div><div class="1"><a href="#"></a></div>"]
// tree (9) ["div class="123"", "p", "p", "input type="text" ", "div", "div class="1"", "a href="#"", "a", "div"]
```
