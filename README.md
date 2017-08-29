# ajax
数据对接的多种形式
1.后台返回一个页面

js代码

/**(1)用$("#content-wrapper").html(data);显示页面*/
$.ajax({
    async : false,
    cache : false,
    type : 'POST',
    url : 'area/prepareCreate',
    error : function() {
        alert('smx失败 ');
    },
    success : function(data) {
        $("#content-wrapper").html(data);
    }
});

Java代码

/*
 * (1)不能有注解@RespoonseBody 
 * (2)返回值类型是String,代表页面所在的文件夹
 * (3)如果返回"error"，执行ajax的error方法
 */
@RequestMapping(value = "area/prepareCreate", method = RequestMethod.POST)
public String editAreaWithFile() {
    return "area/create;
    // return "error";
}

2. 后台返回一个基本类型String,Long等

js代码

$.ajax({
    async : false,
    cache : false,
    type : 'POST',
    url : 'app/area/delete',
    dataType : "json",
    error : function() {
        alert('smx失败 ');
    },
    success : function(data) {
    /**重点：前台接收到返回值，直接处理就行*/
        alert(data);
    }

});

java代码

/**(1)用@ResponseBody注解
(2)可以接收各种参数，url，data(名称匹配或@RequestBody)
(3)返回值类型就是基本类型*/
@ResponseBody
@RequestMapping(value = "area/delete", method = RequestMethod.POST)
public long editArea() {
    //处理参数
    return 3;   
}

3. 后台返回一个实体类

第一步：定义一个实体类

/**
 * flag为1 :操作失败
 * msg：失败原因；
 * flag为0: 操作成功*/
public class AjaxResponseMsg {
    private int flag;
    private String msg; 
}

第二步：js代码

$.ajax({
    async : false,
    cache : false,
    type : 'POST',
    url : 'app/area/delete',
    error : function() {
        alert('smx失败 ');
    },
    success : function(data) {  
        var jsonData = JSON.parse(data);            
        if (jsonData .flag == 0) {//请求成功
            alert("后台操作成果");  
        } else {
            alert(jsonData .msg);  
        }
    }
});

第三步：java代码

    /**（1）添加注解@ResponseBody
    (2)可以接收参数
    (3)返回类型就是实体类*/
    @ResponseBody
    @RequestMapping(value = "area/delete", method = RequestMethod.POST)
    public AjaxResponseMsg editAreaWithFile() {

        // 做操作
        boolean result = true
        AjaxResponseMsg ajaxMsg = new AjaxResponseMsg();
        if (result == true)
            ajaxMsg.setFlag(0);
        else {
            ajaxMsg.setFlag(1);
            ajaxMsg.setMsg("不能给一个传感器添加重复设备");
        }
        return ajaxMsg; // 返回给前台一个实体类，包括是否操作成功，及失败原因
    }

4.后台返回一个实体类list（实体类的字段都是基本类型）

实体类

public class Section{
    private Long id; //id
    private String name; //名称          
}

前台

$.ajax({
    async : false,
    cache : false,
    type : 'POST',

    url : "section/getSections",
    error : function() {
        alert("失败");
    },
    success : function(data) {
        var jsonData = JSON.parse(data); //jsonData是该下路下的所有区间（json格式） 

        for (var i = 0; i < jsonData.length; i++) {
            alert(jsonData[i].id);
            alert(jsonData[i].name);                    
        }

    }
})

后台

@ResponseBody
@RequestMapping("/section/getSections")
@RequiresPermissions("section:view")
public List<Section> getSections() {
    List<Section> sections = new List<Section>();
    return sections;
}

5.后台返回一个实体类list（实体类的字段包括List类型）

第一步：定义实体类ChartSeries

public class ChartSeries {
    private String name; //曲线名称     
    private List<Float> data; //曲线的纵坐标 
    private List<Date> occurTime; //曲线的横坐标 
    //添加get set方法    
}

第二步：js代码

  $.ajax({
            url : "/dataRecord/chart" , //获取数据列 
            type : 'GET',
            data : {
            },
            success : function(data) { 
                formatStringToJson(data);//对数据进行处理
            }
        });

    function formatStringToJson(data) {

        jsonData = JSON.parse(data);  //把后台传来的数据转来JSON格式
        //jsonData是二维数组，因为实体类ChartSeries的字段data也是个list
        for (x in jsonData) {  //遍历JSON格式的数组取元素, x代表下标

            for (y in jsonData[x].data) {                
                 alert(jsonData[x].occurTime[y]) ;//依次获取 
                 alert(jsonData[x].data[y]);
            }

        }


    }


第三步：java代码

@ResponseBody
@RequiresPermissions("dataRecord:view")
@RequestMapping(value = "/dataRecord/chart}", method = RequestMethod.GET)
public List<ChartSeries> getChart(){         
    List<ChartSeries> list =  new List<ChartSeries>();
    //给list赋值
    return list; // list
}

后台传来的数据格式如下 
这里写图片描述
