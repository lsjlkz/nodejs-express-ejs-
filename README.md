# nodejs-express-ejs-经验总结

nodejs+express+ejs 是开发简易后端的全套工具，可动态连接数据库获取或更改内容

部署开发环境后
```
#app.js

//app.js
var mysql = require("mysql");
var indexRouter = require('./routes/index');
var webgisRouter = require('./routes/webgis');//引用动态页面

var mysql = require("mysql");
//db配置
var con = mysql.createConnection({
    host: "localhost", //主机 默认为localhost
    user: "user",
    password: "password",
    database: "webgisdatabase" //数据库名
});
//连接（成功与否在控制台输出信息）
con.connect(function(err) {
    if (err) {
        console.log('connecting error');
        return;
    }
    console.log('connecting success');
});

app.use(function(req, res, next) {
    req.con = con;
    next();
});

app.use('/', indexRouter);
app.use('/webgis', webgisRouter);

app.get('/', function (req, res) {
  res.render('index');//此处几个get有顺序规定
});
app.get('/webgis',function(req, res) {
  res.render('webgis');
});//返回webgis页面

module.exports = app;//打包为app

```
/views文件夹下存放动态页面，后缀为ejs，直接由html更改后缀即可
```
#webgis.ejs
<%for(var key in data){%>     //data为js中连接数据后输出的database数据列表，类型为数组
<%= data[key].idJOB %>//这个是获取js中传递的数据
<%}%>//for循环结束

//ejs传递参数到js中处理有几种方法，我这里用到了两种，url传递和form组件的post方法传递，input输入框中为参数，submit为提交，reset为重置
<form method="post">
<input >
<input type="submit" >
<input type="reset" >
</form>

//url传递方式为url后加?page=11
//如/query?page==11，传递方法为get


```
routes文件夹下是js文件
```
#webgis.js
// use index.ejs
var db = req.con;
    var data = "";

    db.query('SELECT * FROM JOB ', function(err, ans) {
        if (err) {
            console.log(err);
        }else{
        	var data = ans;

        	res.render('webgis', { title: 'Webgis Information', data: data});
console.log(data);//传递data
		console.log("load database successfully");
	}
    });
    
    
router.post('/query',function(req,res,next){//post类型
	console.log("query start");
	var xx = req.body.xx;//通过req.body.xx获取参数
	var db=req.con;
	var data="";
	db.query('SELECT * FROM JOB WHERE JOB_NAME like "' + xx + '";', function(err, ans) {
        		if (err) {
            			console.log(err);
        		}else{
        			var data = ans;
        			res.render('webgis', { title: 'Webgis Information', data: data,xx:xx});
              console.log(data);
				console.log("query successfully");
			}		
  });
});


router.get('/query', function(req, res, next) {//url传递参数
	console.log("query start");
	var page=req.query.page;
)};

module.exports = router;//打包
```
public文件夹下存放静态文件，如css等
