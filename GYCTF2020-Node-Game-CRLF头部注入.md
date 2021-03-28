---
title: '[GYCTF2020]Node Game-CRLF头部注入'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-11-26 12:51:44
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1115648.jpg
---
源码：

    var express = require('express');
    var app = express();
    var fs = require('fs');
    var path = require('path');
    var http = require('http');
    var pug = require('pug');
    var morgan = require('morgan');
    const multer = require('multer');


    app.use(multer({dest: './dist'}).array('file'));
    app.use(morgan('short'));
    app.use("/uploads",express.static(path.join(__dirname, '/uploads')))
    app.use("/template",express.static(path.join(__dirname, '/template')))


    app.get('/', function(req, res) {
        var action = req.query.action?req.query.action:"index";
        if( action.includes("/") || action.includes("\\") ){
            res.send("Errrrr, You have been Blocked");
        }
        file = path.join(__dirname + '/template/'+ action +'.pug');
        var html = pug.renderFile(file);
        res.send(html);
    });

    app.post('/file_upload', function(req, res){
        var ip = req.connection.remoteAddress;
        var obj = {
            msg: '',
        }
        if (!ip.includes('127.0.0.1')) {
            obj.msg="only admin's ip can use it"
            res.send(JSON.stringify(obj));
            return 
        }
        fs.readFile(req.files[0].path, function(err, data){
            if(err){
                obj.msg = 'upload failed';
                res.send(JSON.stringify(obj));
            }else{
                var file_path = '/uploads/' + req.files[0].mimetype +"/";
                var file_name = req.files[0].originalname
                var dir_file = __dirname + file_path + file_name
                if(!fs.existsSync(__dirname + file_path)){
                    try {
                        fs.mkdirSync(__dirname + file_path)
                    } catch (error) {
                        obj.msg = "file type error";
                        res.send(JSON.stringify(obj));
                        return
                    }
                }
                try {
                    fs.writeFileSync(dir_file,data)
                    obj = {
                        msg: 'upload success',
                        filename: file_path + file_name
                    } 
                } catch (error) {
                    obj.msg = 'upload failed';
                }
                res.send(JSON.stringify(obj));    
            }
        })
    })

    app.get('/source', function(req, res) {
        res.sendFile(path.join(__dirname + '/template/source.txt'));
    });


    app.get('/core', function(req, res) {
        var q = req.query.q;
        var resp = "";
        if (q) {
            var url = 'http://localhost:8081/source?' + q
            console.log(url)
            var trigger = blacklist(url);
            if (trigger === true) {
                res.send("<p>error occurs!</p>");
            } else {
                try {
                    http.get(url, function(resp) {
                        resp.setEncoding('utf8');
                        resp.on('error', function(err) {
                        if (err.code === "ECONNRESET") {
                         console.log("Timeout occurs");
                         return;
                        }
                       });

                        resp.on('data', function(chunk) {
                            try {
                             resps = chunk.toString();
                             res.send(resps);
                            }catch (e) {
                               res.send(e.message);
                            }
 
                        }).on('error', (e) => {
                             res.send(e.message);});
                    });
                } catch (error) {
                    console.log(error);
                }
            }
        } else {
            res.send("search param 'q' missing!");
        }
    })

    function blacklist(url) {
        var evilwords = ["global", "process","mainModule","require","root","child_process","exec","\"","'","!"];
        var arrayLen = evilwords.length;
        for (var i = 0; i < arrayLen; i++) {
            const trigger = url.includes(evilwords[i]);
            if (trigger === true) {
                return true
            }
        }
    }

    var server = app.listen(8081, function() {
        var host = server.address().address
        var port = server.address().port
        console.log("Example app listening at http://%s:%s", host, port)
    })

上传功能要求ip为127.0.0.1，remoteaddress无法伪造，所以估计是ssrf。
文件路径中有mimetype，这是可控的，所以存在目录穿越。
接下来就是怎么利用的问题了。
由于nodejs没怎么接触过 解释不清楚 就贴几个师傅的链接了：
https://www.zhaoj.in/read-6462.html
https://www.jianshu.com/p/504621863fa3
https://guokeya.github.io/post/hz6_KR03h/
https://blog.5am3.com/2020/02/11/ctf-node1/#%E8%87%AA%E5%B7%B1%E5%87%BA%E7%9A%84-node-game


还有个+号拼接的我觉得最容易想到吧：
https://blog.csdn.net/qq_42181428/article/details/104474414
以及出题人wp里那个奇怪url编码的原理：
https://xz.aliyun.com/t/2894

上面四个师傅讲的很清楚。