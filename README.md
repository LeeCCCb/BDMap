# 百度地图API在Vue中使用

## 1.申请账号和获取密钥

**账号：百度账号**

**密钥：申请网址：<http://lbsyun.baidu.com/apiconsole/key?application=key>**

**申请完后会得到密钥，如下如中的AK值**

![申请完密钥界面](/img/2.png)

**创建应用--本文只讲解浏览器端的使用**

![选择适用项目](/img/1.png)

## 2.创建地图

**在项目最外层的index.js文件中添加如下script标签，红框中填写上面申请的AK值**

![选择适用项目](/img/3.png)
![选择适用项目](/img/4.png)

**地图组件页面代码如下图所示**

![选择适用项目](/img/5.png)
![选择适用项目](/img/6.png)

**很简单，至此一个地图就能在网页上显示了**

![选择适用项目](/img/7.png)

### 2.1 控件

<table>
    <tr>
        <th>控件</th>
        <th>类名</th>
        <th>简介</th>
    </tr>
    <tr>
        <th>抽象基类</th>
        <th>Control</th>
        <th>所有控件均继承此类的方法、属性。通过此类您可实现自定义控件</th>
    </tr>
    <tr>
        <th>平移缩放控件</th>
        <th>NavigationControl</th>
        <th>PC端默认位于地图左上方，它包含控制地图的平移和缩放的功能。移动端提供缩放控件，默认位于地图右下方</th>
    </tr>
    <tr>
        <th>缩略地图</th>
        <th>OverviewMapControl</th>
        <th>默认位于地图右下方，是一个可折叠的缩略地图</th>
    </tr>
    <tr>
        <th>比例尺</th>
        <th>ScaleControl</th>
        <th>默认位于地图左下方，显示地图的比例关系</th>
    </tr>
    <tr>
        <th>地图类型</th>
        <th>MapTypeControl</th>
        <th>默认位于地图右上方</th>
    </tr>
    <tr>
        <th>版权</th>
        <th>CopyrightControl</th>
        <th>默认位于地图左下方</th>
    </tr>
    <tr>
        <th>定位</th>
        <th>GeolocationControl</th>
        <th>针对移动端开发，默认位于地图左下方</th>
    </tr>
</table>

**可以使用map.addControl()方法向地图添加控件，例如：添加控制器**

```javaScript
map.addControl(new BMap.NavigationControl());
```

### 2.2 控件位置

<table>
    <tr>
        <th>anchor值</th>
        <th>位置说明</th>
    </tr>
    <tr>
        <th>BMAP_ANCHOR_TOP_LEFT</th>
        <th>表示控件定位于地图的左上角</th>
    </tr>
    <tr>
        <th>BMAP_ANCHOR_TOP_RIGHT</th>
        <th>表示控件定位于地图的右上角</th>
    </tr>
    <tr>
        <th>BMAP_ANCHOR_BOTTOM_LEFT</th>
        <th>表示控件定位于地图的左下角</th>
    </tr>
    <tr>
        <th>BMAP_ANCHOR_BOTTOM_RIGHT</th>
        <th>表示控件定位于地图的右下角</th>
    </tr>
</table>

**指定位置配合位置偏移，防止控件之间相互覆盖，代码如下：**

```javaScript
var opts = {offset: new BMap.Size(150, 5)};
map.addControl(new BMap.ScaleControl(opts));
```

### 2.3 自定义控件

**百度地图支持自定义的控件，但是必须继承Control，下面的例子是通过按钮实现放大地图的效果**

```javaScript
// 定义一个控件类，即function
function ZoomControl(){
    // 设置默认停靠位置和偏移量  
    this.defaultAnchor = BMAP_ANCHOR_TOP_LEFT;
    this.defaultOffset = new BMap.Size(10, 10);
}
// 通过JavaScript的prototype属性继承于BMap.Control
ZoomControl.prototype = new BMap.Control();

// 自定义控件必须实现initialize方法，并且将控件的DOM元素返回
// 在本方法中创建个div元素作为控件的容器，并将其添加到地图容器中
ZoomControl.prototype.initialize = function(map){
    // 创建一个DOM元素
    var div = document.createElement("div");
    // 添加文字说明
    div.appendChild(document.createTextNode("放大2级"));
    // 设置样式
    div.style.cursor = "pointer";
    div.style.border = "1px solid gray";
    div.style.backgroundColor = "white";
    // 绑定事件，点击一次放大两级
    div.onclick = function(e){  
        map.zoomTo(map.getZoom() + 2);
    }
    // 添加DOM元素到地图中
    map.getContainer().appendChild(div);
    // 将DOM元素返回  
    return div;
}

// 创建控件实例
var myZoomCtrl = new ZoomControl();
// 添加到地图当中
map.addControl(myZoomCtrl);
```

**效果如图**

![选择适用项目](/img/8.png)

## 3.在地图上绘制

**提供的覆盖物**
<table>
    <tr>
        <th>覆盖物</th>
        <th>类名</th>
        <th>说明</th>
    </tr>
    <tr>
        <th>抽象基类</th>
        <th>Overlay</th>
        <th>所有的覆盖物均继承此类的方法</th>
    </tr>
    <tr>
        <th>点</th>
        <th>Marker</th>
        <th>表示地图上的点，可自定义标注的图标</th>
    </tr>
    <tr>
        <th>文本</th>
        <th>Label</th>
        <th>表示地图上的文本标注，您可以自定义标注的文本内容
</th>
    </tr>
    <tr>
        <th>折线</th>
        <th>Polyline</th>
        <th>表示地图上的折线</th>
    </tr>
    <tr>
        <th>多边形</th>
        <th>Polygon</th>
        <th>表示地图上的多边形。多边形类似于闭合的折线，另外您也可以为其添加填充颜色</th>
    </tr>
    <tr>
        <th>圆</th>
        <th>Circle</th>
        <th>表示地图上的圆</th>
    </tr>
    <tr>
        <th>信息窗口</th>
        <th>InfoWindow</th>
        <th>信息窗口也是一种特殊的覆盖物，它可以展示更为丰富的文字和多媒体信息。注意：同一时刻只能有一个信息窗口在地图上打开</th>
    </tr>
    <tr>
        <th>地面叠加层</th>
        <th>GoundOverlay</th>
        <th>表示叠加在地图上的图片，图片的链接、大小、位置等属性可以自定义</th>
    </tr>
    <tr>
        <th>海量点</th>
        <th>PointCollection</th>
        <th>针对点的数量很大的情况，可以使用海量点进行展示</th>
    </tr>
    <tr>
        <th>自定义覆盖物</th>
        <th>自定义</th>
        <th>支持通过继承覆盖物基类Overlay，自定义覆盖物</th>
    </tr>
</table>

### 3.1 标注

```javaScript
var map = new BMap.Map("container");
var point = new BMap.Point(116.404, 39.915);
map.centerAndZoom(point, 15);
var marker = new BMap.Marker(point);        // 创建标注
map.addOverlay(marker);                     // 将标注添加到地图中
```

**标注点击事件**

```javaScript
marker.addEventListener("click", function(){
    alert("您点击了标注");
});  
```

**标注移动事件**

```javaScript
//enableDragging添加移动 disableDragging取消移动 默认情况下标注不支持拖拽 dragend动作可获取当前位置
marker.enableDragging();
marker.addEventListener("dragend", function(e){
    alert("当前位置：" + e.point.lng + ", " + e.point.lat);
})
```

### 3.2 信息窗口

注：同一时刻只能有一个信息窗口在地图上展开

```javaScript
var opts = {
    width : 250,     // 信息窗口宽度
    height: 100,     // 信息窗口高度
    title : "Hello"  // 信息窗口标题
}
var infoWindow = new BMap.InfoWindow("World", opts);  // 创建信息窗口对象
map.openInfoWindow(infoWindow, map.getCenter());      // 打开信息窗口
```

### 3.3 叠加图层

```javaScript
var traffic = new BMap.TrafficLayer();        // 创建交通流量图层实例
map.addTileLayer(traffic);                    // 将图层添加到地图上
```

```javaScript
map.removeTileLayer(traffic);                // 将图层移除
```

### 3.4 自定义绘制层

```javaScript
var canvasLayer = new BMap.CanvasLayer({
    update: update
});

function update() {
    var ctx = this.canvas.getContext("2d");

    if (!ctx) {
        return;
    }

    ctx.clearRect(0, 0, ctx.canvas.width, ctx.canvas.height);

    var temp = {};
    ctx.fillStyle = "rgba(50, 50, 255, 0.7)";
    ctx.beginPath();
    var data = [
        new BMap.Point(120.17, 30.26)
    ];

    for (var i = 0, len = data.length; i < len; i++) {

        // 绘制时需要对经纬度进行转换
        var pixel = map.pointToPixel(data[i]);

        ctx.fillRect(pixel.x, pixel.y, 100, 100);
    }
}

map.addOverlay(canvasLayer);
```

![选择适用项目](/img/9.png)

## 4.事件

百度地图API中的大部分对象都含有addEventListener方法，您可以通过该方法来监听对象事件。例如，BMap.Map包含click、dblclick等事件。在特定环境下这些事件会被触发，同时监听函数会得到相应的事件参数e，比如当用户点击地图时，e参数会包含鼠标所对应的地理位置point。

### 4.1 事件监听

**弹窗事件**

```javaScript
var map = new BMap.Map("container");
map.centerAndZoom(new BMap.Point(116.404, 39.915), 11);
map.addEventListener("click", function(){
    alert("您点击了地图。");
}
);
```

**捕获状态**

通过监听事件还可以捕获事件触发后的状态。
如下示例，显示用户拖动地图后地图中心的经纬度信息：

```javaScript
var map = new BMap.Map("container");
map.centerAndZoom(new BMap.Point(116.404, 39.915), 11);
map.addEventListener("dragend", function(){
    var center = map.getCenter();
    alert("地图中心点变更为：" + center.lng + ", " + center.lat);
}
);
```

### 4.2 事件参数和this

**通过参数获取有用信息**

```javaScript
map.addEventListener("click", function(e){
    alert(e.point.lng + ", " + e.point.lat);
});
```

**this指向DOM**

```javaScript
map.addEventListener("zoomend", function(){
    alert("地图缩放至：" + this.getZoom() + "级");
});
```

### 4.3 移除监听事件

```javaScript
map.removeEventListener("click", showInfo);
```

## 5.获取地图数据

### 5.1 检索POI

检索POI服务提供三种检索方法：城市检索、圆形区域检索、矩形区域检索。

**城市检索--方法：search()--范围：定点**

```javaScript
var local = new BMap.LocalSearch(map, {
    renderOptions:{map: map}
});
local.search("西湖");
```

![选择适用项目](/img/10.png)

**圆形区域检索--方法：searchNearby()--范围：某点附近圆形区域**

```javaScript
var local = new BMap.LocalSearch(map, {renderOptions: {map: map,autoViewport: true}});
local.searchNearby("小吃", "西湖");
```

![选择适用项目](/img/11.png)

**矩形区域检索--方法：searchInBounds()--范围：视野范围内检索**

```javaScript
var local = new BMap.LocalSearch(map,{ renderOptions:{map: map}});
local.searchInBounds("银行", map.getBounds());
```
![选择适用项目](/img/12.png)

### 5.2 逆/地址解析

**地址解析**

```javaScript
// 创建地址解析器实例
var myGeo = new BMap.Geocoder();
// 将地址解析结果显示在地图上，并调整地图视野
myGeo.getPoint("杭州市江干区学林街16号", function(point){
    if (point) {
        map.centerAndZoom(point, 16);
        map.addOverlay(new BMap.Marker(point));
    }
}, "杭州市");
```

![选择适用项目](/img/13.png)

**逆址解析**

```javaScript
// 创建地址解析器实例
var myGeo = new BMap.Geocoder();
// 将地址解析结果显示在地图上，并调整地图视野
myGeo.getLocation(new BMap.Point(120.17, 30.26), function(result){
    if (result){
        alert(result.address);
    }
});
```

![选择适用项目](/img/14.png)

**鼠标点击获取地址**

```javaScript
// 创建地址解析器实例
var geoc = new BMap.Geocoder();
map.addEventListener("click", function(e){
    var pt = e.point;
    geoc.getLocation(pt, function(rs){
        var addComp = rs.addressComponents;
        alert(addComp.province + ", " + addComp.city + ", " + addComp.district + ", " + addComp.street + ", " + addComp.streetNumber);
    });
});
```

![选择适用项目](/img/15.png)

### 5.3 出行路线

<table>
    <tr>
        <th>路线规划方式</th>
        <th>类名</th>
        <th>简介</th>
    </tr>
    <tr>
        <th>驾车</th>
        <th>DrivingRoute</th>
        <th>提供驾车路线规划服务</th>
    </tr>
    <tr>
        <th>公交</th>
        <th>TransitRoute</th>
        <th>提供市内公交和跨城交通方式（飞机、火车、大巴）的路线规划服务</th>
    </tr>
    <tr>
        <th>步行</th>
        <th>WalkingRoute</th>
        <th>提供步行路线规划服务</th>
    </tr>
    <tr>
        <th>骑行</th>
        <th>RidingRoute</th>
        <th>提供骑行线路规划服务</th>
    </tr>
</table>

**驾车**

```javaScript
var driving = new BMap.DrivingRoute(map, { 
    renderOptions: { 
        map: map, 
        autoViewport: true 
    } 
});
var start = new BMap.Point(120.17, 30.26);
var end = new BMap.Point(120.16, 30.25);
driving.search(start, end);
```

![选择适用项目](/img/16.png)

**公交**

```javaScript
var transit = new BMap.TransitRoute(map, { 
    renderOptions: { 
        map: map, 
        autoViewport: true 
    } 
});
var start = new BMap.Point(120.17, 30.26);
var end = new BMap.Point(120.16, 30.25);
transit.search(start, end);
```

![选择适用项目](/img/17.png)

**步行**

```javaScript
var walk = new BMap.WalkingRoute(map, {
    renderOptions: {map: map}
});
var start = new BMap.Point(120.17, 30.26);
var end = new BMap.Point(120.16, 30.25);
walk.search(start, end);
```

![选择适用项目](/img/18.png)

**骑行**

```javaScript
var riding = new BMap.RidingRoute(map, {
    renderOptions: {map: map}
});
var start = new BMap.Point(120.17, 30.26);
var end = new BMap.Point(120.16, 30.25);
riding.search(start, end);
```

![选择适用项目](/img/19.png)
