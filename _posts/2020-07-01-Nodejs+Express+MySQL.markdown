---
layout: post
title: Nodejs+Express+MySQL数独游戏
date: 2020-07-01 20:51:23 +0300
description: 基于Nodejs、Express和mysql技术的安装和使用以及数据库处理。 # Add post description (optional)
img: /proj1/pic0.png # Add image post (optional)
tags: [Nodejs, Express, MySQL] # add tag
---

## Nodejs 下载安装
我们可以直接从Nodejs官网下载 [how to install nodejs](https://nodejs.org/en/download/).

![photo1]({{site.baseurl}}/assets/img/proj1/pic1.png){:width="80%"}

根据你使用的电脑下载对应的版本，然后安装。Nodejs的安装教程，网上也有很多，可以参考。

安装完成后打开DOS命令，进入Nodejs的安装目录，使用Node-V查看当前下载的版本。

![photo2]({{site.baseurl}}/assets/img/proj1/pic2.png)


## Express框架安装和简单服务器配置
首先创建一个文件夹，然后使用DOS命令进入该目录。

我们先安装全局 Express。 请注意，4.x版本前后全局express命令不同。上一版本安装说明为[ npm install express -g ]，后版本安装说明为[ npm install -g express-generator ]。

![photo3]({{site.baseurl}}/assets/img/proj1/pic3.png)

然后我们继续设置 Express 服务器，我们在其中创建了一些文件夹和项目，并在 DOS 命令中输入了以下说明。
```
md NodeJSProject
cd NodeJSProject
md expressdemo
cd expressdemo
express app
```
App是Express项目的名称，可以自定义。

等待安装，完成后终端会有如下两个提示。

![photo4]({{site.baseurl}}/assets/img/proj1/pic4.png)

我们按照终端提示执行以下指令。
```
cd app
npm install
```
完成后，我们可以看到app里面的文件和文件结构。

![photo21]({{site.baseurl}}/assets/img/proj1/pic21.png){:width="80%"}

至此，我们已经完成设置，可以启动服务器查看了。 在app目录下输入以下指令启动服务器。
```
npm start
```
![photo5]({{site.baseurl}}/assets/img/proj1/pic5.png)

这通常表示启动成功，我们可以在浏览器中查看，浏览器访问 [http://localhost:3000/](http://localhost:3000/).

![photo6]({{site.baseurl}}/assets/img/proj1/pic6.png){:width="80%"}

启动成功，该页面可以解读为Hello word！

这里解释一下Express项目的文件结构：

1.bin :-> 用于启动服务器应用程序。

2.node_modules :-> 存储一些依赖模块。

3.public :-> 存储静态资源。

4.routes :-> 主要用于确定如何响应来自特定端点客户端的请求，包含一个URI（一个标识和定位任何资源的字符串）和一个特定的HTTP请求方法（例如：POST、AXIOS等）。

5.views :-> 存储一些模板文件，您可以自定义这些模板文件来封装一些组件。

6.app.js :-> 文件和服务器启动项。


## Mysql下载安装
相信大家对Mysql很熟悉，用的人也很多，网上教程也很多，这里就不赘述了。直接上mysql官网 [MySQL](https://dev.mysql.com/downloads/mysql/).您可以下载它并根据您的计算机选择版本。

![photo7]({{site.baseurl}}/assets/img/proj1/pic7.png){:width="80%"}

根据提示完成后，在DOS命令中输入如下命令行，查看当前数据库对应信息。
```
mysql -V
or
mysql --version
```
![photo8]({{site.baseurl}}/assets/img/proj1/pic8.png)

数据库结构。

![photo9]({{site.baseurl}}/assets/img/proj1/pic9.png)

我们创建一个名为 db_sudoku 的数据编号，并创建一个表格名称为 score 和五个列名称的表 [ username,timer,checktime,score,id ],它们的属性是 [ char(10),char(10),int(3),int(3),int(3) ].

![photo10]({{site.baseurl}}/assets/img/proj1/pic10.png)

插入以下两列数据。

![photo11]({{site.baseurl}}/assets/img/proj1/pic11.png)


## 继续项目
我们回到我们创建的根目录，我们安装mysql驱动，回到根目录，输入以下指令。
```
npm install mysql
```
![photo12]({{site.baseurl}}/assets/img/proj1/pic12.png)

至此我们的环境和项目基本建立，目录文件结构至此。

![photo13]({{site.baseurl}}/assets/img/proj1/pic13.png)

我们可以开始编写文件和脚本来完成项目。


### 在根目录下创建index.html
```
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no" />
    <title>Main</title>
    <link rel="stylesheet" type="text/css" href="css/main.css" />

</head>

<body>
    <!--Leaderboard content-->
    <div>
        <div>
            <table id="tab" cellpadding="10" cellspacing="15" frame="box">
                <!--单元格调整-->
                <tr>
                    <td><b>排行榜</b></td>
                </tr>

                <tr class="tr">
                    <!--一行-->
                    <!--四列-->
                    <td>username</td>
                    <td>checktime</td>
                    <td>score</td>
                    <td>time</td>
                </tr>
            </table>
        </div>
    </div>

    </br>
    <!-- Submit a list  -->
    <div class="hidden">
        <form action="http://localhost:3001/add" method="GET">
            <label for="">NAME:</label><input type="text" name="upload_name" id="upload_name"></br></br>
            <label for="">TIME:</label><input type="text" name="upload_time" id="upload_time"></br></br>
            <label for="">CHECKTIME:</label><input type="text" name="upload_checktime" id="upload_checktime"></br></br>
            <label for="">SCORE:</label><input type="text" name="upload_score" id="upload_score"></br></br>
            <input type="submit" value="submit" id="s_submit">
        </form>
    </div>

    <script src="http://code.jquery.com/jquery-3.1.1.min.js"></script>
    <script>
        $(function () {
            $.get('http://localhost:3000/', function (data) {
                for (let list of data) {
                    let table = '<tr class="tbd"><td>';
                    let s = '</td><td>';
                    for (let ele of list) {
                        table += (ele + s);
                    }
                    table += '</td></tr>';
                    $("#tab").append(table);
                }
            });
        });
    </script>

</body>

</html>
```
### 修改NodeJSProject/expressdemo/app/routes/index.js
```
const express = require('express');
const router = express.Router();

const db = require("../db");

var id = 1;

router.get('/', function (request, response) {
  var sql = 'SELECT * FROM score';
  var mydata = [];
  db.query(sql, [], (err, rows) => {
    if (err) {
      response.json({ err: "Failed to connect" })
    } else {
      for (let em of rows) {
        console.log(em);
        let record = [em['username'], em['checktime'], em['score'], em['timer']];
        mydata.push(record);
      }
      console.log(mydata);
      response.writeHead(200, {
        "Content-Type": "application/json"
      });
      response.write(JSON.stringify(mydata));
      response.end();
    };
  });

  db.query('SELECT count(*) FROM score', [], (err, rows) => {
    if (err) {
      response.json({ err: "Failed to connect" })
    } else {
      for (let em of rows) {
        id = em['count(*)'] + 1;
      }
      console.log("id:", id);
      console.log(typeof id);
    }
  });

});
module.exports = router;



const fs = require('fs');
const app = express();
app.use(express.static(__dirname));

app.get('/add', function (req, res) {
  var response = {
    "upload_name": req.query.upload_name,
    "upload_time": req.query.upload_time,
    "upload_score": req.query.upload_score,
    "upload_checktime": req.query.upload_checktime
  };

  var addsql = 'INSERT INTO score(username,timer,checktime,score,id) VALUES(?,?,?,?,?)';
  var addsqlparams = [response.upload_name, response.upload_time, response.upload_checktime, response.upload_score, id];
  db.query(addsql, addsqlparams, function (err, result) {
    if (err) {
      console.log('[INSERT ERROR]-');
      res.send('insert error');
      return;
    }
    res.send('submit successfully');
    res.end();
  })
})

var server = app.listen(3001, function () {
  var port = server.address().port
  console.log("Access to the port %s", port);
})
```
### 在NodeJSProject/expressdemo/app/目录下创建一个db.js
```
// Creating a database connection
const mysql = require("mysql");

const connection = mysql.createConnection({
    host: "localhost",
    user: "root",
    password: "root",
    database: "db_sudoku",
    port: 3306
})

connection.connect((err) => {
    if (err) {
        console.log("The connection fails")
    } else {
        console.log("The connection is successful")
    }
})

var query = (sql, params, callback) => {
    connection.query(sql, params, function (err, rows) {
        callback(err, rows);
    });
}

exports.query = query;
```
### 修改app.js
```
app.all("*", function (req, res, next) {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "Content-Type,Content-Length,Authorization,Accept,X-Requested-With");
  res.header("Access-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS");
  res.header("X-Powered-By", '3.2.1');
  if (req.method == "OPTIONS") res.send(200);
  else next();
});
```
上面的代码应该放在 var app = Express() 和 app.use('/', indexRouter) 上面

![photo15]({{site.baseurl}}/assets/img/proj1/pic15.png){:width="50%"}

### 测试
现在让我们看看我们的前端是什么样子的。

![photo14]({{site.baseurl}}/assets/img/proj1/pic14.png){:width="50%"}

现在我们的数据库交互基本完成了，我们可以通过DOS命令进入APP目录，使用指令npm start启动服务器进行测试。然后我们去localhost，就可以看到我们创建的表格内容了 之前设计的，两条数据。

![photo16]({{site.baseurl}}/assets/img/proj1/pic16.png){:width="50%"}

现在我们打开前端，可以看到Express已经启动的数据库的内容。

![photo22]({{site.baseurl}}/assets/img/proj1/pic22.png){:width="50%"}

读取就可以了，接下来看看写入数据库怎么样，所以我们输入如下数据。

![photo17]({{site.baseurl}}/assets/img/proj1/pic17.png){:width="50%"}

表示提交成功。

![photo18]({{site.baseurl}}/assets/img/proj1/pic18.png){:width="50%"}

然后我们回到我们的主页，发现数据已经在前端了。

![photo19]({{site.baseurl}}/assets/img/proj1/pic19.png){:width="50%"}

它已经在数据库中了。

![photo20]({{site.baseurl}}/assets/img/proj1/pic20.png){:width="50%"}


### 结束语
这是一个非常简单的 Nodejs + Express + mysql 小应用程序。 那时我在做这个，因为我有一个项目，而且我很少使用 JS 语言，所以我在这方面比较薄弱。 找了很多资料，也花了很多时间才弄明白。所以在这里做个笔记，和大家分享一下。

你可以在我的仓库中 fork 这个项目。 我稍后会上传到我的仓库。 谢谢！

[https://github.com/setwgx/Nodejs-and-Express-and-mysql-based-front-and-back-end-interactions](https://github.com/setwgx/Nodejs-and-Express-and-mysql-based-front-and-back-end-interactions.git)