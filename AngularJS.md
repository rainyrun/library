# AngularJS

mvc 模式

双向绑定

依赖注入

模块化设计

## 入门程序

页面

```html
<html>
<head>
    <title>入门程序</title>
    <script type="text/javascript" src="angular.min.js"></script>
    <script type="text/javascript">
        // 控制器
        // 建立模块
        var app = angular.module("myApp", []); // []内是引用的其他模块
        // 创建控制器，$scope是视图层与控制层交换数据的桥梁
        app.controller("myController", function($scope){
            // 
            $scope.add=function(){
                return parseInt($scope.x) + parseInt($scope.y);
            };

            $scope.add2=function(){
                $scope.z = parseInt($scope.x) + parseInt($scope.y);
            };

            $scope.list=[1, 2, 3, 4];

            $scope.objectList=[
                {name:"marry", score:100},
                {name:"peter", score:80},
                {name:"lily", score:90}
            ];
        });

        // 内置服务
        app.controller("myController", function($scope, $http){
            $scope.findList=function(){
                $http.get("data.json").success(function(response){
                    // response 是 data.json 里的内容
                    $scope.objectList = response;
                });
            };
        });
    </script>

</head>
<!-- 必须加上ng-app才能识别表达式；ng-init用来初始化变量 -->
<body ng-app ng-init="name='abc'"> 
    <!-- 表达式 -->
    {{100 + 100}}
    <!-- 绑定变量(双向绑定：一处修改，处处同步) -->
    姓名<input type="text" ng-model="name" />
    {{name}}
</body>

<!-- 控制器
    ng-app="myApp" 引入模块；ng-controller="myController"指定控制器
-->
<body ng-app="myApp" ng-controller="myController"> 
    输入x <input type="text" ng-model="x" />
    输入y <input type="text" ng-model="y" />

    {{add()}}
</body>

<!-- 事件指令 -->
<body ng-app="myApp" ng-controller="myController">
    输入x <input type="text" ng-model="x" />
    输入y <input type="text" ng-model="y" />

    <button ng-click="add2()">计算</button>
    结果：{{z}}
</body>

<!-- 循环 -->
<body ng-app="myApp" ng-controller="myController">
    <!-- 数组 -->
    <ul>
        <li ng-repeat="item in list">{{item}}</li>
    </ul>
    <!-- 对象 -->
    <ul>
        <li ng-repeat="entity in objectList">name：{{entity.name}}，score：{{entity.score}} </li>
    </ul>
</body>

<!-- 内置服务 -->
<body ng-app="myApp" ng-controller="myController" ng-init="findList()">
    <ul>
        <!-- $index 获取索引 -->
        <li ng-repeat="entity in objectList">name：{{entity.name}}，score：{{entity.score}}, index：$index </li>
    </ul>
</body>
</html>
```

分层开发

自定义服务

```js
// 定义服务
app.service("someService", function($http){
    this.findAll = function(){
        return $http.get("../brand/findAll.do");
    };

    this.other = function(){};
});
```

控制器继承

```js
// 1. 引入要继承的Controller
// 2. 在控制器内继承
app.controller("myController", function($scope, $controller){
    $controller("要继承的controller", {$scope:$scope});
});
```

为列表增加元素

```js
// 定义变量，对变量初始化
$scope.targetList = [];
$scope.targetList.push({});
// 删除
$scope.targetList.splice(index, 1);
```



