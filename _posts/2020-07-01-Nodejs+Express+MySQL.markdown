---
layout: post
title: Nodejs+Express+MySQL :->(sudoku games)
date: 2020-07-01 20:51:23 +0300
description: Nodejs and Express and mysql technology based installation and use as well as database processing. # Add post description (optional)
img: /proj1/pic0.png # Add image post (optional)
tags: [Nodejs, Express, MySQL] # add tag
---

## Nodejs download and install
We can download it directly from Nodejs's official website [how to install nodejs](https://nodejs.org/en/download/).

![photo1]({{site.baseurl}}/assets/img/proj1/pic1.png){:width="80%"}

Download the corresponding version according to the computer you use, and then install it.Nodejs installation tutorial on the Internet is also a lot of, you can refer to.

After installation, open the DOS command, enter the installation directory of Nodejs, and use Node-V to view the currently downloaded version.

![photo2]({{site.baseurl}}/assets/img/proj1/pic2.png)


## Express framework installation and simple server setup
First create a folder, then use the DOS command to enter the directory.

Let's first install the global Express. Please note that the global express command is different before and after the 4.x version.The previous version installation instruction is [ npm install express -g ],The post - version installation instruction is [ npm install -g express-generator ].

![photo3]({{site.baseurl}}/assets/img/proj1/pic3.png)

We then proceeded to set up the Express server, where we created some folders and projects and entered the following instructions into the DOS command.
```
md NodeJSProject
cd NodeJSProject
md expressdemo
cd expressdemo
express app
```
App is the name of the Express project, which can be customized.

Waiting for the installation, the terminal will have two prompts as follows after completion.

![photo4]({{site.baseurl}}/assets/img/proj1/pic4.png)

We follow the terminal prompt and execute the following instructions.
```
cd app
npm install
```
After finishing, we can see the files inside the app and the file structure.

![photo21]({{site.baseurl}}/assets/img/proj1/pic21.png){:width="80%"}

At this time, we have completed the setup and can start the server for viewing. Enter the following instructions in the directory of app to start the server.
```
npm start
```
![photo5]({{site.baseurl}}/assets/img/proj1/pic5.png)

This usually means the startup is successful and we can view it in the browser,browser access [http://localhost:3000/](http://localhost:3000/).

![photo6]({{site.baseurl}}/assets/img/proj1/pic6.png){:width="80%"}

Successfully launched, this page can be interpreted as Hello word！

Here's an explanation of the file structure of the Express project：

1.bin:-> Used to start the server application.

2.node_modules:-> Store some dependency modules.


3.public:-> Storing static resources.

4.routes:->Mainly used to determine how to respond to a request from a particular endpoint client,Contains a URI(a string that identifies and locates any resource) and a specific HTTP request method (for example: POST, AXIOS, and so on).

5.views:-> Store some template files that you can customize to encapsulate some components.

6.app.js:-> File and server startup entry.


## Mysql download and install
I believe you are familiar with Mysql, and you have a lot of people using it, there are a lot of online tutorials, so I won't go into details here.Directly on mysql's official website [MySQL](https://dev.mysql.com/downloads/mysql/).You can download it and select the version based on your computer.

![photo7]({{site.baseurl}}/assets/img/proj1/pic7.png){:width="80%"}

After completing according to the prompt, enter the following command line in the DOS command to view the current database corresponding information.
```
mysql -V
or
mysql --version
```
![photo8]({{site.baseurl}}/assets/img/proj1/pic8.png)

Database structure.

![photo9]({{site.baseurl}}/assets/img/proj1/pic9.png)

We create a data number called db_sudoku and create a table with the form name score and five column names [ username,timer,checktime,score,id ],Their attributes are [ char(10),char(10),int(3),int(3),int(3) ].

![photo10]({{site.baseurl}}/assets/img/proj1/pic10.png)

Insert the following two columns of data.

![photo11]({{site.baseurl}}/assets/img/proj1/pic11.png)


## Continue with the project
We go back to the root directory we created, we install the mysql driver, go back to the root directory, and enter the following instructions.
```
npm install mysql
```
![photo12]({{site.baseurl}}/assets/img/proj1/pic12.png)

At this point our environment and project is basically established, the directory file structure at this point.

![photo13]({{site.baseurl}}/assets/img/proj1/pic13.png)

We can start writing files and scripts to complete the project.


### Create index.html in the root directory
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
### Modify NodeJSProject/expressdemo/app/routes/index.js
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
### Under the NodeJSProject/expressdemo/app/ create a db. Js
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
### Modify app.js
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
The above code should be placed under var app = Express () and above app.use('/', indexRouter)

![photo15]({{site.baseurl}}/assets/img/proj1/pic15.png){:width="50%"}

### TEST
Now let's see how our front end looks.

![photo14]({{site.baseurl}}/assets/img/proj1/pic14.png){:width="50%"}

Now that we've basically finished our database interaction, we can test it by entering the APP directory in the DOS command and using the instruction NPM Start to start the server.And then we go to localhost, and we see the table content that we designed earlier, two pieces of data.

![photo16]({{site.baseurl}}/assets/img/proj1/pic16.png){:width="50%"}

Now we open the front end, which can see the contents of the database now that Express has been started.

![photo22]({{site.baseurl}}/assets/img/proj1/pic22.png){:width="50%"}

Reading is ok, so let's see how about writing to the database, so let's enter the following data.

![photo17]({{site.baseurl}}/assets/img/proj1/pic17.png){:width="50%"}

Indicates successful submission.

![photo18]({{site.baseurl}}/assets/img/proj1/pic18.png){:width="50%"}

Then we go back to our home page and find that the data is already on the front end.

![photo19]({{site.baseurl}}/assets/img/proj1/pic19.png){:width="50%"}

It's in the database already.

![photo20]({{site.baseurl}}/assets/img/proj1/pic20.png){:width="50%"}


### CONCLUSION
This is a very simple Nodejs + Express + mysql small application. At that time, I was doing this because I had a project, and I seldom used JS language, so I was relatively weak in this aspect. I found a lot of materials and spent a lot of time to figure this out.So I'm going to make a note here, and I'm going to share it with you.

You can fork this project in my warehouse. I will upload it to my warehouse later. Thank you!

[Project link](https://github.com/mccartyzzz/Nodejs-and-Express-and-mysql-based-front-and-back-end-interactions.git)