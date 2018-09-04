# FAQ

### lov 如何映射多个字段？

```javascript
// form
{
    type:'popup',
    options:{    
        onSelected:function(args){
            // for form
            xx_form.setData({xxx:args.data[0].xxx});
        }
    }
}

// grid
{
    editor:{
        type:'popup',
        onSelected:function(args){
            this.options.host_grid_row.xxx = args.data[0].xxx;  
        }
    }
}
```

### select 如何映射多个字段？

```javascript
// form
{
    type:'select',
    options:{
        onSelected:function(value,name,item){
            xx_form.setData({xxx:item.xxx});
        }
    }
}

// grid
{
    editor:{
        type:'select',
        onSelected:function(value,name,item){
            this.options.host_grid_row.xxx = item.xxx;
        }
    }
}
```

### grid 更改内容不会马上显示效果?

```javascript
  //更改指定单元格的内容(推荐)
  xx_grid.updateCell('xxx','value',rowid);//xxx 为列名
  //更改整行内容
  xx_grid.updateRow(rowdata,newdata);
  //直接刷新整个 grid
  xx_grid.reRender();
  //刷新指定行
  xx_grid.reRender({rowdata:rowdata});
```

### grid 如何控制单元格 或 列的可编辑性

```javascript
  onBeforeEdit:function(item){
      if(item.column.columnname=='lang'){
          // 控制整列
          return false;
      }
      if(item.column.columnname=='name'&&item.record.name=='AAA'){
          // 控制单元格
          return false;
      }
      return true;
  }
```

### grid date 编辑器格式化？

```javascript
  {
      type:'date',
      format:'yyyy-MM-dd',
      editor:{
          type:'date'
      }
  }
  // 注意有两个 type:'date'
```

### grid 里面怎么实现 checkbox?

```javascript
{
    render:Hap.createGridCheckBoxRender()
}
// 默认使用 Y,N 表示 checkValue,uncheckValue
//也可以通过参数指定其他值
{
    render:Hap.createGridCheckBoxRender({
        checkValue:'TRUE',
        uncheckValue:'FALSE'
    })
}
```

