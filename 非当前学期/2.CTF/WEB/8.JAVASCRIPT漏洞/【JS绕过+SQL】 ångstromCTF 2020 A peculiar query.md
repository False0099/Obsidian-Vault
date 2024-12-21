## 步骤：
首先，我们发现，我们可以查看我们的 SQL 源码，我们于是可以去查看我们的对应的 JS 源码，查看对应的内容：
```js
const express = require("express");
const rateLimit = require("express-rate-limit");
const app = express();
const { Pool, Client } = require("pg");
const port = process.env.PORT || 9090;
const path = require("path");

const client = new Client({
	user: process.env.DBUSER,
	host: process.env.DBHOST,
	database: process.env.DBNAME,
	password: process.env.DBPASS,
	port: process.env.DBPORT
});

async function query(q) {
	const ret = await client.query(`SELECT name FROM Criminals WHERE name ILIKE '${q}%';`);
	return ret;
}

app.set("view engine", "ejs");

app.use(express.static("public"));

app.get("/src", (req, res) => {
	res.sendFile(path.join(__dirname, "index.js"));
});

app.get("/", async (req, res) => {
	if (req.query.q) {
		try {
			let q = req.query.q;
			// no more table dropping for you
			let censored = false;
			for (let i = 0; i < q.length; i ++) {
				if (censored || "'-\".".split``.some(v => v == q[i])) {
					censored = true;
					q = q.slice(0, i) + "*" + q.slice(i + 1, q.length);
				}
			}
			q = q.substring(0, 80);
			const result = await query(q);
			res.render("home", {results: result.rows, err: ""});
		} catch (err) {
			console.log(err);
			res.status(500);
			res.render("home", {results: [], err: "aight wtf stop breaking things"});
		}
	} else {
		res.render("home", {results: [], err: ""});
	}
});

app.listen(port, function() {
	client.connect();
	console.log("App listening on port " + port);
});

출처: [https://eine.tistory.com/entry/2020-angstromCTF-web-write-up](https://eine.tistory.com/entry/2020-angstromCTF-web-write-up) [아인스트라세의 SW 블로그:티스토리]
```
我们上面内容的含义就是：**将我们的对应的内容进行对应的字符过滤，并且把对应的字符转换为对应的其他符号**。但是，他**没有考虑到我们的 q 如果是一个数组**我们应该怎么样去处理。

我们考虑到，我们可以通过对于我们的原本的构造的 payload 的任意截取，从而来绕过我们的要求，因为我们的**字符串是不等于我们的单个字符的**。例如，我们可以构造：
```
["test01",'\\\\'','or '1'=1'','a-a'];
```

但是，我们注意到，我们的后续还有我们的对应的**取 substr 操作**，如果我们只是传入一个数组，我们的对应的内容会被我们呢的对应转换为*。从而造成我们的**字符逃逸**，于是，我们可以设置我们的对应的内容如下：
```
["123456789","0","0","0","0","0","0","0","0","0","0","'"]
```
这样，我们就可以让我们的对应的字符产生逃逸，从而让我们获得对应的结果。



